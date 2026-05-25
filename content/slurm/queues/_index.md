---
date: '2026-03-24T18:43:16-03:00'
draft: false
title: 'Jobs e Filas'
weight: 10
cascade:
    type: docs
---


O slurm configura o cluster com um conjunto de filas (queues). Os jobs de cada fila são executados, em primeira aproximação, um após o outro, na ordem de submissão em cada fila. As filas servem para, por exemplo, separar os nós que contém tipos de processadores ou algum acelerador diferente, por grupos de usuários, por prioridades de usuários, por tempo limite de execução, e muitos outros esquemas. Como o Zumbi é pequeno, relativamente simples e com usuários relativamente inocentes, temos apenas uma fila de submissão e você não precisa se preocupar com isto.
O slurm chama as filas de "partitions".

# `sinfo` -- Filas e nós

O comando [`sinfo`](https://slurm.schedmd.com/sinfo.html) fornece uma visão geral das filas e nós disponíveis no cluster.

```console
[ramiro@zumbi ~]$ sinfo --summarize
PARTITION AVAIL  TIMELIMIT   NODES(A/I/O/T) NODELIST
normal*      up 5-00:00:00          0/3/0/3 n[01-03]
[ramiro@zumbi ~]$
```

Vemos que, neste instante de tempo, o Zumbi tem uma única fila, chamda "normal", que está disponível, (up), cujo limite de tempo de execução é de 5 dias, não tem nenhum nó alocado  (A=0), tem três nós completamente desocupados, no modo "idle" (I=3), de um total de três nós (T=3), e finalmente o nós que estão nesta fila, n01, n02 e n03. Este comando com estas opções não reporta processadores, apenas nós completos, portante não é particularmente útil para entender como está o uso da máquina. Neste caso é melhor você usar um comando como
```console
[ramiro@zumbi ~]$ sinfo -o "%.10n %15C"
 HOSTNAMES CPUS(A/I/O/T)
       n01 0/128/0/128
       n02 0/128/0/128
       n03 0/128/0/128
[ramiro@zumbi ~]$
```
Que mostra o estado das CPUs em cada nó, com a mesma convenção acima. É claro que lembrar estas opções de formatação é pouco prático, então é recomendável criar um alias para isto, por exemplo, colocando no seu arquivo .bashrc ou .bash_aliases algo como
```console
alias cpus='sinfo -o "%.10n %15C"'
```

# `squeue` -- Jobs
Para saber o que está rodando e esperando para rodar no cluster, você usa o comando [`squeue`](https://slurm.schedmd.com/squeue.html). Por exemplo
```console
[ramiro@zumbi ~]$ squeue
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
              2588    normal    sleep   ramiro  R       0:06      1 n01
              2587    normal    sleep   ramiro  R       1:21      1 n01
[ramiro@zumbi ~]$ 
```
Sem nenhum parâmetro, este comando mostra o jobid, que é u número do job e é como o slurm identifica cada job que está sendo executado no sistema, em qual fila está rodando, ou esperando para executar, o que é redundante por enquanto no Zumbi, pois só temos uma fila. Em seguida temos o nome do job, que claramente pode ser repetido, o dono do job, o estado do job, "R", no caso, há quanto tempo ele está rodando e a lista *do nós* no qual o job está executando. Percebam que não há menção sobre quantos processadores estão em uso por cada job no comando `squeue` sem opções.

O estado do job pode ser "R", que significa que ele está rodando no momento. Há muitos flags e você precisa olhar o manual para conhecer todos, mas entre os principais, estã́o "F", se a execução falhou por algum motivo, "PD", para "pending", se o job está na fila, esperando a liberação de recursos para ser executado, "CG", quando o job terminou com sucesso e o slurm está terminando de resolver a burocracia e "CD", quando o job completou com sucesso.

Eu tenho um alias para este comando com opções que eu acho mais úteis.
```console
[ramiro@zumbi ~]$ sq
 JOBID      USER CPUS TIME_LIMIT  TIME_LEFT ST NODELIST
  2589    ramiro   88    2:00:00    1:59:50  R n01
  2587    ramiro    1    2:00:00    1:37:36  R n01
[ramiro@zumbi ~]$ 
```
O alias `sq` é 
```console
alias sq='squeue --format "%.6A %.9u %.4C %.10l %.10L %.2t %N"'
```

Agora que sabemos com examinar as filas, vamos ver como podemos [lançar](../enque) jobs no cluster.

