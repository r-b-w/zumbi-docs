---
date: '2026-03-24T18:43:16-03:00'
draft: false
title: 'Jobs MPI'
weight: 30
cascade:
    type: docs
---

Para que um programa paralelo escrito com MPI funcione corretamente, em paralelo, com todos os processos gerenciados pelo slurm, e usando da maneira mais eficiente possível o hardware de comunicação do  sistema, *um monte* de bibliotecas precisam estar instaladas corretamente e ser compatíveis entre si.

Não é óbvio que um binário précompilado em algum outro sistema vá funcionar corretamente no Zumbi, não importa o quão semelhante as arquiteturas destes sistemas possam parecer com a do Zumbi. Assim, a forma mais garantida de ter um programa paralelo baseado em MPI rodando corretamente no Zumbi é compilá-lo a partir do código fonte usando os compiladores e bibliotecas disponíveis no Zumbi. Há uma grande variedade de compiladores instalados e é fácil escolher entre eles, como está descrito na seção se [software](/software). Por enquanto, vamos imaginar que magicamente já existe um compilador C adequadamente configurado.

O intuito desta seção *não é*  de forma alguma ensinar MPI. Apenas queremos mostrar como executar um programa MPI gerenciado pelo slurm, e para isto precisamos usar alguma coisa que garantidamente funcione.

# Programa exemplo
Vamos usar como exemplo um programa em C, muito simples, que cria um conjunto com 1 ou mais  processos, e a partir de um deles, envia para todos os processos uma mesma mensagem, simultaneamente, realizando portanto um "broadcast". O código fonte do programa segue, e vamos considerá-lo salvo no arquivo "bcast.c".

```c 
#include <mpi.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main(int argc, char** argv) {
    int rank, size;
    int broadcast_value = 0;
    char hostname[256];

    // Initialize the MPI environment
    MPI_Init(&argc, &argv);

    // Get the rank (ID) of the current process and total number of processes
    MPI_Comm_rank(MPI_COMM_WORLD, &rank);
    MPI_Comm_size(MPI_COMM_WORLD, &size);

    // Get the hostname of the compute node
    gethostname(hostname, 256);

    // Root process (rank 0) defines the value to broadcast
    if (rank == 0) {
        broadcast_value = 99; // Value to broadcast
        printf("Root process (Rank 0) on host '%s' is broadcasting value: %d\n", hostname, broadcast_value);
    }

    // Broadcast the value from rank 0 to all other processes
    MPI_Bcast(&broadcast_value, 1, MPI_INT, 0, MPI_COMM_WORLD);

    // Every process prints the value it received
    printf("Process %d on host '%s' received broadcasted value: %d\n", rank, hostname, broadcast_value);

    // Finalize the MPI environment
    MPI_Finalize();
    return 0;
}
```
Se você não entende nada deste código fonte considere-se sortudo.

# Compilação
Vamos supor que você tem o programa anterior salvo em um diretório
```console 
[ramiro@zumbi tmp2]$ ls
bcast.c
```

Conforme dissemos anteriormente, é recomendável que inclusive as suas compilações e testes não sejam feitas no nó de login, e sim em um dos nós computacionais. Para um programa simples como este, e com o cluster descarregado, quando temos a garantia que não vamos ficar aguardando na fila, podemos fazer simplesmente,

```console 
[ramiro@zumbi tmp2]$ srun mpicc -o bcast bcast.c
[ramiro@zumbi tmp2]$ 
```
Os compiladores do linux tendem a ser discretos e não avisar nada quando corre tudo bem, o que foi o caso aqui.  No entanto, podemos ver que a compilação funcionou, pois foi criado um executável
```console 
[ramiro@zumbi tmp2]$ ls -l
total 24
-rwxr-xr-x 1 ramiro ramiro 17752 mai 25 16:29 bcast
-rw-r--r-- 1 ramiro ramiro  1096 mai 25 16:28 bcast.c
[ramiro@zumbi tmp2]$ 
```
O arquivo "bcast" é executável, e, para maiores detalhes, 
```console 
[ramiro@zumbi tmp2]$ file ./bcast
./bcast: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, not stripped
[ramiro@zumbi tmp2]$ 
```
o que definitivamente mostra que é um arquivo binário, com a arquitetura correta.

Só por curiosidade mórbida, podemos ver quais bibliotecas serão usadas quando este arquivo for executado,
```console 
[ramiro@zumbi tmp2]$ ldd ./bcast
	linux-vdso.so.1 (0x00007fffe77f4000)
	libmpi.so.40 => /opt/ohpc/pub/mpi/openmpi5-gnu14/5.0.7/lib/libmpi.so.40 (0x00007fa552400000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fa552000000)
	libopen-pal.so.80 => /opt/ohpc/pub/mpi/openmpi5-gnu14/5.0.7/lib/libopen-pal.so.80 (0x00007fa552818000)
	libfabric.so.1 => /opt/ohpc/pub/mpi/libfabric/1.18.0/lib/libfabric.so.1 (0x00007fa552278000)
	librdmacm.so.1 => /lib64/librdmacm.so.1 (0x00007fa5527f8000)
	libefa.so.1 => /lib64/libefa.so.1 (0x00007fa5527e8000)
	libibverbs.so.1 => /lib64/libibverbs.so.1 (0x00007fa552250000)
	libpsm2.so.2 => /lib64/libpsm2.so.2 (0x00007fa551f90000)
	libucp.so.0 => /opt/ohpc/pub/mpi/ucx-ohpc/1.18.0/lib/libucp.so.0 (0x00007fa551ea8000)
	libucs.so.0 => /opt/ohpc/pub/mpi/ucx-ohpc/1.18.0/lib/libucs.so.0 (0x00007fa551e38000)
	libucm.so.0 => /opt/ohpc/pub/mpi/ucx-ohpc/1.18.0/lib/libucm.so.0 (0x00007fa552230000)
	libuct.so.0 => /opt/ohpc/pub/mpi/ucx-ohpc/1.18.0/lib/libuct.so.0 (0x00007fa551df8000)
	libpmix.so.2 => /opt/ohpc/admin/pmix/lib/libpmix.so.2 (0x00007fa551c00000)
	libm.so.6 => /lib64/libm.so.6 (0x00007fa551b20000)
	libevent_core-2.1.so.7 => /lib64/libevent_core-2.1.so.7 (0x00007fa551ae0000)
	libevent_pthreads-2.1.so.7 => /lib64/libevent_pthreads-2.1.so.7 (0x00007fa5527e0000)
	libhwloc.so.15 => /opt/ohpc/pub/libs/hwloc/lib/libhwloc.so.15 (0x00007fa551a80000)
	libgcc_s.so.1 => /opt/ohpc/pub/compiler/gcc/14.2.0/lib64/libgcc_s.so.1 (0x00007fa551a50000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fa552958000)
	libnl-3.so.200 => /lib64/libnl-3.so.200 (0x00007fa551a28000)
	libnl-route-3.so.200 => /lib64/libnl-route-3.so.200 (0x00007fa551988000)
	libnuma.so.1 => /lib64/libnuma.so.1 (0x00007fa552220000)
	libxml2.so.2 => /lib64/libxml2.so.2 (0x00007fa5517f8000)
	libz.so.1 => /lib64/libz.so.1 (0x00007fa5517d8000)
	liblzma.so.5 => /lib64/liblzma.so.5 (0x00007fa5517a8000)
```

Vejam a quantidade de bibliotecas que este programa ridiculamente simples, que não faz praticamente nada, precisa para rodar, sendo que todas precisam trabalhar em concerto, compatíveis entre si. Não se preocupe se a lista de bibliotecas que aparecer no seu caso for diferente, seu programa pode ser sido compilado com versões diferentes de compiladores e bibliotecas.

Você, em princípio, não precisa se preocupar com nada disto, se não houve uma mensagem de erro de compilação, pode seguir com a vida, estamos examinando estes detalhes por motivos didáticos.

# Execução
Vamos ver como executar este programa interativamente e no modo batch. 

## One shot 
Para rodar este programa uma única vez, no modo interativo, com 16 processadores, na esperança de que o slurm consiga os recursos imediatamente, você pode fazer simplesmente

```console 
[ramiro@zumbi tmp2]$ srun --ntasks=16 ./bcast
Process 15 on host 'n01' received broadcasted value: 99
Process 7 on host 'n01' received broadcasted value: 99
Process 9 on host 'n01' received broadcasted value: 99
Process 5 on host 'n01' received broadcasted value: 99
Process 2 on host 'n01' received broadcasted value: 99
Process 6 on host 'n01' received broadcasted value: 99
Process 10 on host 'n01' received broadcasted value: 99
Process 11 on host 'n01' received broadcasted value: 99
Process 8 on host 'n01' received broadcasted value: 99
Process 12 on host 'n01' received broadcasted value: 99
Process 3 on host 'n01' received broadcasted value: 99
Root process (Rank 0) on host 'n01' is broadcasting value: 99
Process 0 on host 'n01' received broadcasted value: 99
Process 14 on host 'n01' received broadcasted value: 99
Process 1 on host 'n01' received broadcasted value: 99
Process 4 on host 'n01' received broadcasted value: 99
Process 13 on host 'n01' received broadcasted value: 99
[ramiro@zumbi tmp2]$ 
```
Se vocês tem experiência com outros sistemas, notem que não é necessário usar "mpiexec", "mpirun" ou algo assim. Basta rodar o programa MPI com "srun" e o número de processos que você deseja, e tudo funciona automaticamente.

Neste caso, todos os processos caíram no nó 1, que estava, no instante em que o programa foi executado, muito descarregado. Isto, em geral, é uma coisa boa, porque a comunicação interprocessos no mesmo nó é muito rápida. 

# Shell iterativo

Neste caso, em primeiramente reservamos um shell interativo, com um conjunto de processadores alocado, por um certo intervalo de tempo

```console
[ramiro@zumbi tmp2]$ salloc --ntasks=16 --time=03:00:00
salloc: Granted job allocation 2655
salloc: Nodes n01 are ready for job
[ramiro@n01 tmp2]$
```
Notem que o shell foi criado no nó n01. Poderia ter sido criado em qualquer nó do sistema. Muita atenção: *não há garantia* que todos os processos tenham sido criado no mesmo nó! Não conte com este factoide na lógica do seu programa.

Poderíamos também ter feito isto antes de compilar o programa, e neste caso, a compilação seria feita diretamente no nó alocado, sem usar o srun.
```console
[ramiro@n01 tmp2]$ mpicc -o bcast bcast.c 
[ramiro@n01 tmp2]$ 
```
Uma configuração interessante é alocar para uso interativo um pequeno número de processadores, apenas para compilação  preparação de dados, mas rodar o seu programa paralelo em uma alocação separada, muito preferencialmente no modo batch, onde você reserva um grande número de processadores.

Para compilação de programas MPI, é melhor alocar um shell interativo do que usar srun diretamente na linha de comando do nó de login, já que a alocação em si sempre demora um pouco, e você sempre corre o risco de não ter processadores disponíveis na hora exata em que precisa deles.

Podemos executar o programa da mesma maneira,

```console
[ramiro@n01 tmp2]$ srun  ./bcast
Process 14 on host 'n01' received broadcasted value: 99
Process 15 on host 'n01' received broadcasted value: 99
Process 2 on host 'n01' received broadcasted value: 99
Process 10 on host 'n01' received broadcasted value: 99
Process 11 on host 'n01' received broadcasted value: 99
Process 13 on host 'n01' received broadcasted value: 99
Root process (Rank 0) on host 'n01' is broadcasting value: 99
Process 0 on host 'n01' received broadcasted value: 99
Process 4 on host 'n01' received broadcasted value: 99
Process 1 on host 'n01' received broadcasted value: 99
Process 7 on host 'n01' received broadcasted value: 99
Process 9 on host 'n01' received broadcasted value: 99
Process 3 on host 'n01' received broadcasted value: 99
Process 12 on host 'n01' received broadcasted value: 99
Process 5 on host 'n01' received broadcasted value: 99
Process 6 on host 'n01' received broadcasted value: 99
Process 8 on host 'n01' received broadcasted value: 99
[ramiro@n01 tmp2]$ 
```
Notem que não é necessário sequer especificar o número de tarefas, o slurm usa todos os processadores alocados automaticamente.

Você não precisa usar todos os processos que alocou, mas, se não está usando, não deveria ter reservado tantos.

```console
[ramiro@n01 tmp2]$ srun --ntasks=4 ./bcast
Process 1 on host 'n01' received broadcasted value: 99
Process 3 on host 'n01' received broadcasted value: 99
Root process (Rank 0) on host 'n01' is broadcasting value: 99
Process 0 on host 'n01' received broadcasted value: 99
Process 2 on host 'n01' received broadcasted value: 99
[ramiro@n01 tmp2]$ 
```
Não tente usar mais processos do que você alocou, você vai ficar esperando para sempre, ou vai ter um erro de timeout.

# Modo batch
No modo batch precisamos de um arquivo de submissão. Neste exemplo, vamos criá-lo com o nome "bcast_batch.sh", e com o conteúdo
```bash
#!/bin/bash

#SBATCH --ntasks=12
#SBATCH --time=00:15:00
#SBATCH --output=%x-%j.log

srun ./bcast
```

Note que estamos pedindo 12 processadores, 15 minutos de execução e estamos mudando o nome do arquivo que guarda as impressões "na tela" de todos os processos. "%x" é substituído pelo nome do job e "%j" pelo job id.

Mais uma vez, executamos o programa paralelo com "srun", sem especificar o número de processadores. Podemos também usar aqui um número menor que o requisitado na variável de controle do sbatch, e inclusive é possível executar mais de um programa paralelo, sequencialmente ou em paralelo, dentro de um batch. Estes casos são deixados como exercício para o leitor.

Antes de submeter o arquivo ao slurm, vamos registrar o estado do diretório,

```console
[ramiro@zumbi tmp2]$ ls
bcast  bcast_batch.sh  bcast.c
[ramiro@zumbi tmp2]$ 
```
e submetemos o arquivo batch ao slurm com "sbatch",
```console
ramiro@zumbi tmp2]$ sbatch bcast_batch.sh
Submitted batch job 2657
[ramiro@zumbi tmp2]$ sq
 JOBID      USER CPUS TIME_LIMIT  TIME_LEFT ST NODELIST
[ramiro@zumbi tmp2]$
```
Como eu demorei muito para digitar "sq", o job já tinha sido executado e a fila estava vazia. Eu posso confirmar que o job havia sido executado verificando o diretório,
```console
[ramiro@zumbi tmp2]$ ls
bcast  bcast_batch.sh  bcast_batch.sh-2657.log  bcast.c
[ramiro@zumbi tmp2]$
```
O arquivo de log do job foi criado com o  nome "bcast_batch.sh-2657.log", como esperado.

Examinando o conteúdo deste arquivo, temos
```console
[ramiro@zumbi tmp2]$ cat bcast_batch.sh-2657.log 
Process 3 on host 'n01' received broadcasted value: 99
Process 6 on host 'n01' received broadcasted value: 99
Process 5 on host 'n01' received broadcasted value: 99
Process 1 on host 'n01' received broadcasted value: 99
Process 7 on host 'n01' received broadcasted value: 99
Process 8 on host 'n01' received broadcasted value: 99
Process 10 on host 'n01' received broadcasted value: 99
Process 9 on host 'n01' received broadcasted value: 99
Root process (Rank 0) on host 'n01' is broadcasting value: 99
Process 0 on host 'n01' received broadcasted value: 99
Process 11 on host 'n01' received broadcasted value: 99
Process 4 on host 'n01' received broadcasted value: 99
Process 2 on host 'n01' received broadcasted value: 99
[ramiro@zumbi tmp2]$ 
```
que mostra que o programa paralelo foi executado corretamente.

