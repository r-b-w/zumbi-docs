---
date: '2026-03-24T18:43:16-03:00'
draft: false
title: 'Submissão de jobs'
weight: 20
cascade:
    type: docs
---

Há três modos diferentes de submissão de jobs ao cluster, dois modos interativos e um modo 'batch'. Em todos os casos o seu  job vai para uma fila, se os recursos que você solicitou estiverem disponíveis, o seu job começa a executar imediatamente, caso contrário, fica enfileirado esperando os recursos se tornarem disponíveis. O principal recurso gerenciado no Zumbi é o número de CPUs.

 - *Sequencial:* Não há nenhuma espécie de paralelismo, você precisa apenas de uma CPU. Apesar de aparentemente não fazer muito sentido usar o cluster para jobs sequenciais, alguma característica dos nós do cluster, como memória disponível grande, pode justificar este uso.
- *Sequencial com múltiplas instâncias:* Você precisa rodar múltiplas instâncias de jobs sequenciais, tipicamente, múltiplas cópias do mesmo programa com parâmetros diferentes. Isto é conhecido como paralelismo trivial, já que os programas são independentes entre si e não necessidade de comunicação.
- *Paralelo com memória compartilhada:*  O seu programa é paralelo e foi escrito com OpenMP  ou alguma biblioteca de multithreading. Cada thread é um processo do sistema operacional, e é alocada a um processador, e cada processador é alocada a um único processo. Todos os processadores precisam ser alocados *no mesmo nó*. Veremos a seguir como garantir isto, e não é requisitando nós. Nunca faça isto.
- *Paralelo com memória distribuída:* O programa paralelo foi escrito com MPI ou algum outro mecanismo de troca de mensagens. O programam é composto por processos independente que se comunicam por troca de mensagens. Novamente, há uma alocação biunívoca entre processos e processadores, mas os processadores podem ser alocados em nós distintos, e neste caso as mensagens são trocadas pela rede Infiniband. Quando os processos são alocados no mesmo nó, a troca de mensagens é feita, em princípio, estando tudo instalado correta e compativelmente, simplesmente pelo redirecionamento de apontadores na memória,o que é muito rápido, então a alocação de processadores no mesmo é preferível, quando possível.
- *Paralelo híbrido:* É um programa que foi escrito com MPI e OpenMP ou uma biblioteca de threads. Pode ser executado em nós diferentes, desde que todos os processos que compartilham memória via OpenMP estejam alocados ao mesmo nó.


Também é possível submeter ao slurm um job composto de várias instâncias de programas paralelos, com o mesmo ou diferentes modelos de computação paralela. A ideia fundamental é sempre a mesma: um processo, um processador, lembrando que definimos anteriormente que um processador corresponde a um núcleo de uma CPU.

O slurm está configurado no Zumbi baseado na seguinte convenção: um programa paralelo é composto por *tarefas*, e cada tarefa pode ser composta por um ou vários *processos*. Cada tarefa pode ser executada em nós diferentes, mas uma tarefa só pode ser executada em um único nó. Assim, existe uma mapeamento muito natural entre o tipo de programa e o objeto do slurm:
- Sequencial: uma tarefa composta por um processo;
- Memória compartilhada: uma tarefa composta por vários processos;
- Memória distribuída: várias tarefas, cada um composta por um processo;
- Híbrido: várias tarefas, cada uma composta por vários processos.

Todos os comandos de submissão de jobs usam as mesmas opções para especificar para o slurm o número de tarefas e o número de processos em cada tarefa, `--ntasks` e `--cpus-per-task`, respectivamente. Há muitas outras opções para solicitar recursos de CPUs, não as use a menos que você saiba muito bem o que está fazendo.

Nos exemplos abaixo vou usar os comando `sleep` do sistema operacional, que não faz nada, só espera sem consumir recursos, pelo tempo especificado e `hostname`, que imprime o nome do computador no qual o programa está rodando. É claro que no seu caso você vai especificar o programa que você quer executar.

# `srun`
Vamos supor que você queira rodar um programa no cluster rapidamente, só para testar alguma coisa, um programa que vai rodar por no máximo alguns minutos e não exige muita configuração. A maneira mais conveniente é usando o comando [`srun`](https://slurm.schedmd.com/srun.html). Por exemplo, para executar um programa sequencial,
```console
[ramiro@zumbi ~]$ srun hostname
n01
[ramiro@zumbi ~]$

```
Percebam que eu estava no nó de login, mas o comando foi executado no nó n01.

Para executar um programa de memória compartilhada, com 16 threads,
```console
[ramiro@zumbi ~]$ srun --ntasks=1 --cpus-per-task=16 hostname
n01
[ramiro@zumbi ~]$

```
Apenas um processo foi criado, mas 16 CPUs foram alocadas para o job, como podemos ver com este outro comando
```console
[ramiro@zumbi ~]$ srun --ntasks=1 --cpus-per-task=16 /usr/bin/sleep 600

```
Se você digitar este comando, o seu prompt vai ficar bloqueado por 10 minutos, (você pode digitar `<Ctrl-C>` 2 vezes em sequência rapidamente para cancelar o job). Se eu verificar a fila, em outro terminal, temos,
```console
[ramiro@zumbi ~]$ sq
 JOBID      USER CPUS TIME_LIMIT  TIME_LEFT ST NODELIST
  2596    ramiro   16    2:00:00    1:59:54  R n01
[ramiro@zumbi ~]$
```
Onde vemos que foram realmente alocados 16 processadores para este job, que deve usá-las via OpenMP ou threads.

Para executar um programa MPI, podemos fazer
```console
[ramiro@zumbi ~]$ srun --ntasks=16 hostname
n01
n01
n01
n01
n01
n01
n01
n01
n01
n01
n01
n01
n01
n01
n01
n01
[ramiro@zumbi ~]$
```
Por sorte todos os processos foram alocados no mesmo nó, isto *não é garantido*.

Finalmente, para executar um programa híbrido, com 5 processos MPI, cada um usando 16 thread, necessitando portanto de 80 processadores, podemos fazer

```console
[ramiro@zumbi ~]$ srun --ntasks=5 --cpus-per-task=16 /usr/bin/sleep 600
```
Novamente, o console vai ficar bloqueado por 10 minutos ou até que eu digite `<Ctrl-C>` duas vezes. verificando de outra janela,
```console
[ramiro@zumbi ~]$ sq
 JOBID      USER CPUS TIME_LIMIT  TIME_LEFT ST NODELIST
  2598    ramiro   80    2:00:00    1:59:53  R n01
[ramiro@zumbi ~]$ 
```
Onde vemos os 80 processos alocados. Novamente todos foram alocados no nó n01, que estava completamente descarregado, mas a única garantia é que eles serão alocados em blocos de 16, que podem terminar em nós diferentes.

# `salloc`
O comando [`salloc`](https://slurm.schedmd.com/salloc.html) fornece para você um shell interativo em um nó, e um conjunto de processadores, que podem estar no mesmo nó ou em nós diferentes, exatamente como para o comando `srun`. Por exemplo, se eu quiser um shell onde eu posso rodar programas de memória compartilhada com até 16 processadores, posso fazer
```console
[ramiro@zumbi ~]$ salloc --ntasks=1 --cpus-per-task=16
salloc: Granted job allocation 2599
salloc: Nodes n01 are ready for job
[ramiro@n01 ~]$
```
Neste caso, é como se eu tivesse logado em um computador pessoal com 16 CPUs, reservadas para mim. Eu posso fazer tudo aqui, editar, compilar e executar programas. Este é basicamente o modo como eu mais trabalho no cluster na fase de desenvolvimento de um programa paralelo, quando você está compilando e rodando coisas grandes, mas que tipicamente demoram pouco. Você tem acesso direto ao seu diretório raiz, todos os seus arquivos e diretórios são acessíveis de forma transparente, apesar de acesso ser via rede Infiniband, portanto mais lento do que o acesso direto ao disco.
```console
[ramiro@n01 ~]$ ls
Downloads  NovelPorousFormulation_TSha.pdf  benchmarks     data  examples  man               src   tmp
Makefile   Work                             config.status  doc   libtool   run_pvserver.txt  test
[ramiro@n01 ~]$ 
```

Aqui fica fácil verificar o seu diretório de scratch, discutido na seção [Storage](/storage).
```console
[ramiro@n01 ~]$ echo $SLURM_JOB_USER
ramiro
[ramiro@n01 ~]$ echo $SLURM_JOB_ID
2599
[ramiro@n01 ~]$ cd /scratch/$SLURM_JOB_USER.$SLURM_JOB_ID
[ramiro@n01 ramiro.2599]$ touch I_will_die_when_the_job_ends
[ramiro@n01 ramiro.2599]$ ls
I_will_die_when_the_job_ends
[ramiro@n01 ramiro.2599]$ cd 
[ramiro@n01 ~]$ ls /scratch/$SLURM_JOB_USER.$SLURM_JOB_ID
I_will_die_when_the_job_ends
[ramiro@n01 ~]$
```
Este diretório scratch é removido automaticamente quando o job termina, neste caso, quando você encerra o shell, com `exit`, `logout` ou `<Ctrl-D`. Este diretório está montado em um disco SSD e o acesso é muito rápido. A ideia geral é que você requisita um shell, muda para o diretório de scratch, copia os dados para lá, faz as suas coisas  e copia os resultado de volta para a sua pasta raiz, preferencialmente para o diretório /data. 

Atenção, se o seu job tiver alocado tarefas em nós diferentes, *cada nó tem um diretório de scratch independente*, em discos diferentes, e os processos de um nó não conseguem acessar o disco do outro nó. Por inacreditável que pareça, isto é uma boa coisa, pois permite o acesso aos discos de forma paralela, mas isto é bem complexo, e só siga este caminho se você estiver muito certo do que você está fazendo.


Manipulando as opções `--ntasks` e  `--cpus-per-task` você pode adequar o seu shell ao tipo de programa paralelo que você quer rodar. Entenda que o shell que você está rodando é um programa sequencial que está rodando em um único processador em um único nó. O seu programa paralelo é quem tem a responsabilidade de recrutar os outros processos alocados para você para ao seu job.

# `sbatch`
Os dois métodos anteriores são interativos, você tem que ficar sentado na frente do terminal até o programa terminar. Programas paralelos podem ter tempos de execução medidos em dias ou semanas, e, em um sistema ocupado, podem demorar dias na fila, esperando recursos para executar. Claramente um esquema de submissão não interativa, *em batch*, é necessário. O slurm usa o comando [`sbatch`](https://slurm.schedmd.com/sbatch.html) para esta finalidade. 

Para usar o `sbatch`, você precisa criar um arquivo de submissão, que é um arquivo de comandos do shell do sistema, '/bin/bash' no nosso caso. Vamos ver um exemplo simples para rodar um programa de memória compartilhada.

```bash
#!/bin/bash

#SBATCH --ntasks=1
#SBATCH --cpus-per-task=16
#SBATCH --time=12:00:00

echo "job $SLURM_JOB_ID started running at $(date)"
/usr/bin/sleep 300
echo "job $SLURM_JOB_ID started finished running  at $(date)"
```

Este programa só vai alocar 16 processadores em um mesmo nó, e dormir sem fazer nada por 5 minutos. Vamos salvar estes comandos em um arquivo com o nome 'batch_01.sh'.

Você usa o comando `sbatch` para submeter este job.
```console
ramiro@n01 tmp]$ sq
 JOBID      USER CPUS TIME_LIMIT  TIME_LEFT ST NODELIST
  2599    ramiro   16    2:00:00       9:12  R n01
[ramiro@n01 tmp]$ sbatch batch_01.sh
Submitted batch job 2600
[ramiro@n01 tmp]$ sq
 JOBID      USER CPUS TIME_LIMIT  TIME_LEFT ST NODELIST
  2600    ramiro   16   12:00:00   11:59:51  R n01
  2599    ramiro   16    2:00:00       8:39  R n01
[ramiro@n01 tmp]$ cpus
 HOSTNAMES CPUS(A/I/O/T)
       n01 32/96/0/128
       n02 0/128/0/128
       n03 0/128/0/128
[ramiro@n01 tmp]$
```
Vemos que já havia um job em execução, com ID 2599. Submetemos o job com comando sbatch, e o sistema informa o ID do novo job, neste caso, 2600. Para verificar se deu tudo certo, já que podem ocorrer inúmeros problemas na submissão do job, verificamos a fila de jobs, e aí vemos que o novo job está em execução, com 16 processadores.

Ao verificamos a fila um certo tempo depois, temos,

```console
[ramiro@zumbi ~]$ sq
 JOBID      USER CPUS TIME_LIMIT  TIME_LEFT ST NODELIST
[ramiro@zumbi ~]$
```
que mostra que todos os jobs em execução foram encerrados.

Como os jobs no modo batch não estão conectados a um terminal, não tem telas ou teclados associados a ele, não podendo receber entradas do teclado ou imprimir nada na tela. As saídas que iriam ser impressas na tela são direcionadas automaticamente pelo slurm para um arquivo de texto, que, caso você não mude o nome padrão, é chamado 'slurm-numero_do_job.out', por exemplo,
```console
[ramiro@zumbi tmp]$ ls slurm-2600.out 
slurm-2600.out
[ramiro@zumbi tmp]$ cat slurm-2600.out 
job 2600 started running at Fri May 22 13:11:52 -03 2026
job 2600 started finished running  at Fri May 22 13:16:52 -03 2026
[ramiro@zumbi tmp]$ 
```

No arquivo de submissão, as linhas com comentários que começam com '#SBATCH' são interpretadas pelo slurm como opções dados ao comando na linha de comando. Por exemplo, o arquivo abaixo

```bash
#!/bin/bash

echo "job $SLURM_JOB_ID started running at $(date)"
/usr/bin/sleep 300
echo "job $SLURM_JOB_ID started finished running  at $(date)"
```
se salvo com o nome 'batch_02.sh', poderia ser submetido com o comando
```console
[ramiro@zumbi ~]$ sbatch --ntasks=1 --cpus-per-task=16 --time=12:00:00 batch_02.sh
```
e o efeito teria sido exatamente o mesmo. Você pode combinar os dois tipos de opção, mas se você especificar a mesma opção com valores diferentes na linha de comando e como comentário no arquivo de submissão, o valor na linha de comando tem preferência.

## Opção `--time`
Um dos recursos mais importantes gerenciados pelo slurm é o tempo de execução do job. Todo o processo submetido tem um tempo limite. Caso você não especifique este tempo, o sistema atribui automaticamente o tempo limite de 2 horas. 

Quando o job excede o seu tempo limite, com uma pequena tolerância, o job é terminado pelo slurm, forçosamente, sem a menor preocupação com salvamento de arquivos, em qual etapa o job está, nada. A priori parece uma boa ideia então pedir tempos de execução limites exageradamente longos, para não correr o risco de ter um processo interrompido antes de seu fim normal. Isto não é uma boa ideia, no entanto, porque o slurm pode "furar a fila", passando um job que está esperando recursos na frente de um outro que também está esperando recursos, se ele for muito mais rápido do que aquele que está a sua frente.

O ideal então é você pedir o um tempo limite apertado, mas com alguma folga. É óbvio que nas primeiras vezes que você executa um programa novo com dados novos em uma configuração nova, esta estimativa é difícil, mas com o tempo e experiência você consegue chegar a valores razoáveis. Na dúvida, é melhor pecar por excesso. Se a fila do cluster está muito vazia, isto realmente não faz diferença.

A sintaxe desta opção é na média, `--time=DD-HH:MM:SS`, onde DD são dias, HH hora, MM minutos e SS segundos, mas há muitas variações possíveis. Por favor veja o help de qualquer um dos comandos de submissão.

# `scancel`
Somos todos humanos e às vezes cometemos erros. Você pode interromper um job que está executando, desde que você tenha permissão para isto, com  [`scancel`](https://slurm.schedmd.com/scancel.html). O uso básico é óbvio e imediato. Vamos submeter um job e examinar a fila.
```console
[ramiro@zumbi tmp]$ sbatch batch_01.sh
Submitted batch job 2642
[ramiro@zumbi tmp]$ sq
 JOBID      USER CPUS TIME_LIMIT  TIME_LEFT ST NODELIST
  2642    ramiro   16   12:00:00   11:59:46  R n01
```
Vamos cancelar o job 2642, e rapidamente examinar a fila novamente.
```console
[ramiro@zumbi tmp]$ scancel 2642
[ramiro@zumbi tmp]$ sq
 JOBID      USER CPUS TIME_LIMIT  TIME_LEFT ST NODELIST
  2642    ramiro   16   12:00:00   11:59:37 CG n01
```
Notem que estado do job mudou de 'R' para 'CG', o que significa que o job terminou e o sistema está sendo limpo.  Pouco tempo depois, se examinarmos a fila novamente, não há mais vestígio do job.
```console
[ramiro@zumbi tmp]$ sq
 JOBID      USER CPUS TIME_LIMIT  TIME_LEFT ST NODELIST
```
No entanto, o sistema registra a intervenção no arquivo de saída do job.
```console
[ramiro@zumbi tmp]$ cat slurm-2642.out 
job 2642 started running at Fri May 22 16:32:55 -03 2026
slurmstepd-n01: error: *** JOB 2642 ON n01 CANCELLED AT 2026-05-22T16:33:17 ***
[ramiro@zumbi tmp]$ 
```
# Job Arrays
Alguns jobs paralelos caracterizam-se por rodar exatamente o mesmo programa com entradas diferentes. Isto ocorre, por exemplo, quando se faz alguma espécie de análise paramétrica. Neste caso o slurm tem uma funcionalidade especial, [Job arrays](https://slurm.schedmd.com/job_array.html)

Job arrays só funcionam com arquivos de submissão, usando o comando `sbatch`, portanto. Vamos examinar o arquivo de submissão a seguir,
que está salvo sob o nome 'batch_array.sh'
```bash
#!/bin/bash

#SBATCH --ntasks=1
#SBATCH --cpus-per-task=1
#SBATCH --time=1:00:00

echo "job $SLURM_JOB_ID started running at $(date)"
echo "this is task $SLURM_ARRAY_TASK_ID, running on node $(hostname)"
/usr/bin/sleep 120
echo "task $SLURM_ARRAY_TASK_ID finished running  at $(date)"
```
Perceba que podemos diferenciar cada instância sendo executada com a variável de ambiente `$SLURM_ARRAY_TASK_ID`.  Você pode usar isto para fazer com que o seu programa de análise abra arquivos diferentes, numerados sequencialmente, por exemplo. A submissão de um job array é muito simples,

```console
[ramiro@zumbi tmp]$ sbatch --array=0-40%10 batch_array.sh
Submitted batch job 2601
```
A opção `--array=0-40%10` indica que eu quero criar um array com 41 jobs, indexados de 0 a 40, mas com no máximo 10 instâncias executando simultaneamente. Esta última restrição é opcional, e você pode deixar o slurm gerenciar diretamente todo o array como se fossem jobs normais. Não faça isto se o seu array tem milhares de instâncias, no entanto.

Vamos usar o comando `squeue` normal para ver a fila. Naquele alias que definimos anteriormente, não aparecem os detalhes do job array.

```console
[ramiro@zumbi tmp]$ squeue
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
   2601_[10-40%10]    normal batch_ar   ramiro PD       0:00      1 (JobArrayTaskLimit)
            2601_0    normal batch_ar   ramiro  R       0:18      1 n01
            2601_1    normal batch_ar   ramiro  R       0:18      1 n01
            2601_2    normal batch_ar   ramiro  R       0:18      1 n01
            2601_3    normal batch_ar   ramiro  R       0:18      1 n01
            2601_4    normal batch_ar   ramiro  R       0:18      1 n01
            2601_5    normal batch_ar   ramiro  R       0:18      1 n01
            2601_6    normal batch_ar   ramiro  R       0:18      1 n01
            2601_7    normal batch_ar   ramiro  R       0:18      1 n01
            2601_8    normal batch_ar   ramiro  R       0:18      1 n01
            2601_9    normal batch_ar   ramiro  R       0:18      1 n01
[ramiro@zumbi tmp]$
```
Cada job agora é identificado com o número do job e o seu índice no array. Cada elemento job array vai criar um arquivo de saída separado, por padrão.
```console
[ramiro@zumbi tmp]$ ls
batch_01.sh        slurm-2601_18.out  slurm-2601_28.out  slurm-2601_38.out
batch_array.sh     slurm-2601_19.out  slurm-2601_29.out  slurm-2601_39.out
slurm-2601_0.out   slurm-2601_1.out   slurm-2601_2.out   slurm-2601_3.out
slurm-2601_10.out  slurm-2601_20.out  slurm-2601_30.out  slurm-2601_40.out
slurm-2601_11.out  slurm-2601_21.out  slurm-2601_31.out  slurm-2601_4.out
slurm-2601_12.out  slurm-2601_22.out  slurm-2601_32.out  slurm-2601_5.out
slurm-2601_13.out  slurm-2601_23.out  slurm-2601_33.out  slurm-2601_6.out
slurm-2601_14.out  slurm-2601_24.out  slurm-2601_34.out  slurm-2601_7.out
slurm-2601_15.out  slurm-2601_25.out  slurm-2601_35.out  slurm-2601_8.out
slurm-2601_16.out  slurm-2601_26.out  slurm-2601_36.out  slurm-2601_9.out
slurm-2601_17.out  slurm-2601_27.out  slurm-2601_37.out
[ramiro@zumbi tmp]$
```

Vamos ver alguns destes arquivos.
```console
[ramiro@zumbi tmp]$ more slurm-2601_35.out 
job 2637 started running at Fri May 22 16:00:55 -03 2026
this is task 35, running on node n02
task 35 finished running  at Fri May 22 16:02:55 -03 2026
[ramiro@zumbi tmp]$ more slurm-2601_22.out 
job 2624 started running at Fri May 22 15:58:52 -03 2026
this is task 22, running on node n01
task 22 finished running  at Fri May 22 16:00:52 -03 2026
[ramiro@zumbi tmp]$
```

# MPI
Jobs MPI tem algumas sutilezas e merecem uma discussão [separada](../mpi).
