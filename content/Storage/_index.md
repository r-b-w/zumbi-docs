---
draft: true
title: 'Storage'
weight: 30
cascade:
    type: docs
---

A ideia mais importante sobre o armazenamento no zumbi é que todos (praticamente) seus arquivos são acessíveis diretamente de qualquer computador do cluster, isto é, tanto do nó de login quanto de cada um dos nós computacionais. Por exemplo,

```console
[ramiro@zumbi ~]$ ls -C 
benchmarks     data  Downloads  libtool   man                              run_pvserver.txt  test  Work
config.status  doc   examples   Makefile  NovelPorousFormulation_TSha.pdf  src               tmp
[ramiro@zumbi ~]$
```
Não se preocupe em entender o comando a seguir ainda. Eu estou pedindo para que o sistema rode o comando `ls -C` em *algum* nó computacional do cluster, não importa qual, mas certamente não será no nó de login.
*Praticamente nunca* você deve especificar um nó de execução pelo nome, o sistema deve alocar processadores para seu uso no computador onde eles estiverem disponíveis. Temos então,
```console
[ramiro@zumbi ~]$ srun ls -C
Downloads                        benchmarks     examples          src
Makefile                         config.status  libtool           test
NovelPorousFormulation_TSha.pdf  data           man               tmp
Work                             doc            run_pvserver.txt
[ramiro@zumbi ~]$ 
```
A formatação está diferente, mas são os mesmos arquivos. Não sei, e não faz diferença, em qual dos nós computacionais o comando `ls` foi executado.


Como os discos magnéticos estão fisicamente conectados a um computador específico, os demais computadores acessam os arquivos através de um sistema de arquivo remoto, no caso, o NFS versão 4, usando uma interface de conexão Infiniband (IB). Mesmo sendo a tecnologia IB muito veloz, a conexão direta de um disco ao barramento do computador ainda é mais veloz, portanto exite uma diferença de velocidade de acesso. Tipicamente a manipulação de arquivos no nó de login é mais rápida. Isto deve ser encarado mais como uma curiosidade científica, já que você **não deve** rodar nada que demore muito tempo no nó de login.

## Diretório raiz pessoal
Quando você loga no Zumbi, o comportamento padrão é que o seu diretório atual seja o seu diretório raiz pessoal, que é, tipicamente, `/home/${USER}`. `USER` é uma variável de ambiente do sistema operacional Linux que guarda o seu nome de usuário. No meu caso, por exemplo,

```console
[ramiro@zumbi ~]$ echo ${USER}
ramiro
[ramiro@zumbi ~]$ pwd
/home/ramiro
[ramiro@zumbi ~]$
```

O seu diretório raiz pessoal é chamado normalmente de "home", e fica também armazenado em um variável do ambiente, para sua conveniência.
```console
[ramiro@zumbi ~]$ echo ${HOME}
/home/ramiro
[ramiro@zumbi ~]$
```
Nesta documentação vamos tentar manter alguma consistência, mas é provável que usemos os termos "home", "HOME", "$HOME" e "diretório raiz" intercambiavelmente. Todos os diretórios raiz são subdiretório do diretório do sistema `/home`, e estão em um disco relativamente pequeno.


Para dificultar que um usuário distraído ocupe todo o disco e impeça o acesso dos outros usuários, introduzimos *quotas de uso de disco*, que limitam o volume de dados que cada usuário pode armazenar. Para verificar como está a sua situação, proceda como mostrado abaixo,
```console
[ramiro@zumbi ~]$ quota -s -v
Disk quotas for user ramiro (uid 1000):
     Filesystem   space   quota   limit   grace   files   quota   limit   grace
/dev/mapper/almalinux-home
                  1627M    100G    110G           65989       0       0
```
Preocupe-se com a coluna *space*, que mostra quanto armazenamento você está ocupando, e com a coluna *quota*, que mostra quanto você tem disponível. A partir deste valor você recebe um aviso, e o seu limite concreto é o valor mostrado na coluna *limit*.

Você deve usar o seu diretório raiz e subdiretórios dele, exceto um diretório específico que mencionaremos a seguir, para guardar códigos fonte, arquivos de entrada e coisas do gênero. Tipicamente você *não* deve rodar análises que gerem saídas grandes aqui.

## /data

Há uma área de armazenamento bem maior que aquela associada ao diretório `/home`, localizada no diretório `/data`. Neste diretório deve existir um subdiretório com o seu nome de usuário, por exemplo, no meu caso,

```console
ramiro@zumbi ~]$ ls /data/${USER}
opt  var  work
[ramiro@zumbi ~]$
```
Os diretórios `opt`,  `var` e  `work` foram criados por mim e são muito grandes, provavelmente excedem a minha quota no diretório `/home`. No seu caso este diretório deve estar vazio, até que você guarde alguma coisa nele.

Não há cotas no diretório `/data` **ainda**. Vamos tentar manter um comportamento civilizado para que possamos adiar esta ativação o máximo possível.

Quando as contas de usuário são criadas no Zumbi, é criado um link simbólico, correspondente a um "atalho" do Windows, do seu subdiretório em `/data` para o seu diretório raiz, com o nome `data`.

```console
[ramiro@zumbi ~]$ ls ${HOME}/data
opt  var  work
[ramiro@zumbi ~]$ 
```
Assim, você nem precisa se lembrar que a área "grande" está localizada no diretório `/data`. Simplesmente mude para o subdiretório `data` no seu diretório raiz e faça todas as suas análises lá.

Você pode estar curioso pela razão de não criarmos diretamente os diretórios raízes diretamente dentro de `/data`. Há motivos práticos, históricos e operacionais para isto, não é apenas burrice.

## Scratch

Esta seção só fara completo sentido quando você aprender a lançar programas paralelos no cluster. 

Todas as formas de armazenamento que discutimos acima são discos magnéticos que são acessados via uma rede de alta velocidade quando seus programas estão rodando nos nós computacionais, o que **sempre** será o caso. A combinação destes dois fatores, mesmo considerando que a rede IB é relativamente rápida, faz que a escrita e leitura de arquivos não seja a coisa mais rápida do universo.

Para mitigar um pouco este problema, cada nó computacional tem um disco SSD que é usado com área de scratch, com duas características importantes:

1. Cada um dos discos é acessível apenas a partir do nó no qual ele está conectado.
2. O armazenamento nos discos scratch é *transiente*. É criado um diretório para cada job paralelo que é executado, e este diretório e todo o seu conteúdo são automaticamente removidos quando o job termina.

A primeira característica implica em que você não pode trocar informações entre processos via arquivos escritos nos diretórios de scratch.

A segunda característica implica em que você precisa copiar explicitamente os dados de entrada para o seu disco de scratch, se quiser lê-los de lá, e precisa explicitamente copiar do disco de scratch para algum subdiretório do seu diretório raiz antes do job paralelo terminar. Nos scripts exemplo há exemplos sobre como fazer isto. Sem tentar entender completamente o que está acontecendo aqui, temos

```console
[ramiro@zumbi ~]$ # <-- Estamos no nó de login
[ramiro@zumbi ~]$ salloc --ntasks=1 
salloc: Granted job allocation 2554
salloc: Nodes n01 are ready for job
[ramiro@n01 ~]$ # <-- Estamos agora no nó n01
[ramiro@n01 ~]$ cd /scratch/${SLURM_JOB_USER}.${SLURM_JOB_ID}
[ramiro@n01 ramiro.2555]$ pwd
/scratch/ramiro.2555
[ramiro@n01 ramiro.2555]$ # Este diretório só existe enquanto o job paralelo estiver ativo
[ramiro@n01 ramiro.2555]$ touch I_will_die_soon
[ramiro@n01 ramiro.2555]$ ls
I_will_die_soon
[ramiro@n01 ramiro.2555]$ 
exit
salloc: Relinquishing job allocation 2555
salloc: Job allocation 2555 has been revoked.
[ramiro@zumbi ~]$ # De volta ao nó de login, job paralelo encerrado
[ramiro@zumbi ~]$ sudo ssh n01 ls /scratch
[sudo] senha para ramiro: 
diogo.1678
diogo.1679
leandro_aguiar.2273
mateus.895
rafael_vasconcelos.1658
rafael_vasconcelos.1718
rafael_vasconcelos.1856
rafael_vasconcelos.2524
soraia_silva.1827
[ramiro@zumbi ~]$ # Notem a ausência do diretório ramiro.2555 !
[ramiro@zumbi ~]$ 
```

Note que um usuário normal não pode, em princípio, acessar diretamente um nó computacional via secure shell, eu tive que fazê-lo como super usuário.

A maneira correta de acessar o diretório scratch é usando o nome `/scratch/${SLURM_JOB_USER}.${SLURM_JOB_ID}`. Não tente adivinhar o nome do diretório, deixe o sistema fazer isto para você. Lembre-se que este nome será diferente a cada execução de um job paralelo.

Para cada job paralelo é criado, e posteriormente removido automaticamente, um diretório independente. Não há maneira simples de dois jobs paralelos distintos acessarem o mesmo diretório scratch.

Um job paralelo pode ser composto de várias tarefas, que podem ser distribuídas por nós computacionais distintos. Neste caso, as tarefas que são executadas em um nó acessam o diretório scratch daquele nó, mas não acessam os diretórios das tarefas que estão rodando em outros nós, porque estão em discos diferentes. 

## TLDR

Resumindo: faça todas as suas análises em `${HOME}/data` ou subdiretórios criados dentro dele.

Seria interessante agora ter uma pequena noção sobre como pode ser feita a [execução de programas](/slurm) paralelos no Zumbi.
