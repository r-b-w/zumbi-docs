---
date: '2026-03-24T18:43:16-03:00'
draft: false
title: 'Spack'
weight: '20'
cascade:
    type: docs
---

O [Spack](https://spack.io/https://spack.io/) é um sistema de gerenciamento de pacotes e ambientes para programação científica. É um sistema *extremamente sofisticado*. É uma das maravilhas da natureza. Não é uma coisa que vamos explicar em algumas poucas linhas.  
Nesta seção vamos mostrar um exemplo simples do uso do Spack para instalar um pacote científico.


O Spack foi desenvolvido, principalmente, para ser usado por um pesquisador ou grupo, que cria um ambiente específico para um projeto, onde todas as dependências são mantidas automaticamente e compatíveis entre si, muito semelhante, e não por coincidência, aos [ambientes virtuais](https://docs.python.org/pt-br/3/library/venv.html) usados na linguagem Python . 

A ideia é que o grupo instale o Spack em um diretório compartilhado para o grupo e instale os programas e bibliotecas que este projeto precisa usando o próprio Spack. Como o spack, por default, instala os programas a partir do código fonte, e, por default, compila  os programas para  a arquitetura do processador no qual está rodando, com opções de otimização agressivas, (isto é configurável também), os programas executáveis tendem a ser mais rápidos do que os mesmos programas instalados de uma distribuição Linux geral, ou via OpenHPC. Isto também reforça a necessidade de alocar um shell interativo em um nó computacional e não fazê-lo no nó de login.

## Instalação para o usuário
O Spack é um sistema bem grande, que descarrega muita coisa. É **muito importante** que você faça sua instalação local em um diretório com muito espaço, tipicamente, o diretório "/home/${USER}/data", conforme [discutido anteriormente](/storage).

Vamos clonar o repositório do Spack.
```console
[ramiro@zumbi data]$ git clone -c feature.manyFiles=false https://github.com/spack/spack.git ~/data/spack-local
Cloning into '/home/ramiro/data/spack-local'...
remote: Enumerating objects: 677525, done.
remote: Counting objects: 100% (1029/1029), done.
remote: Compressing objects: 100% (350/350), done.
remote: Total 677525 (delta 842), reused 679 (delta 676), pack-reused 676496 (from 4)
Receiving objects: 100% (677525/677525), 235.38 MiB | 18.71 MiB/s, done.
Resolving deltas: 100% (327496/327496), done.
[ramiro@zumbi data]$ ls spack-local/
bin           CITATION.cff  etc  LICENSE-APACHE  NEWS.md  pyproject.toml  README.md    share
CHANGELOG.md  COPYRIGHT     lib  LICENSE-MIT     NOTICE   pytest.ini      SECURITY.md  var
[ramiro@zumbi data]$
```
É interessante fazer um update do repositório do Spack com `git pull` dentro deste diretório.

Vamos criar um diretório de configuração do Spack
```console
[ramiro@zumbi data]$ cd ~
[ramiro@zumbi ~]$ mkdir .spack
[ramiro@zumbi ~]$ cd .spack
[ramiro@zumbi .spack]$
```
Perceba que como o nome do diretório começa com um ponto, é um diretório invisível, que só aparece em uma listagem de diretório se você usar o flag "-a".

Vamos configurar a instalação local para aproveitar o que a instalação global do sistema já tem pronto. Precisamos criar o arquivo "~/.spack/upstreams.yaml", com o conteúdo

```yaml
upstreams:
  system-spack:
    install_tree: /opt/ohpc/pub/apps/spack/local
```
Vamos torcer para que a versão que instalamos "na mão" seja compatível com a que veio OpenHPC, que é bem mais antiga. Temos configurar o nosso shell para usar a configuração local do Spack. É necessário adicionar as linhas abaixo no final do seu arquivo de configuração do bash, "~/.bashrc"

```bash
export SPACK_ROOT=$HOME/data/spack-local
source $SPACK_ROOT/share/spack/setup-env.sh
```
Saia do sistema e entre novamente. Depois de logar no Zumbi novamente, verifique se deu tudo certo.
```console
[ramiro@zumbi ~]$ which spack
spack ()
{ 
    : this is a shell function from: /home/ramiro/data/spack-local/share/spack/setup-env.sh;
    : the real spack script is here: /home/ramiro/data/spack-local/bin/spack;
    _spack_shell_wrapper "$@";
    return $?
}
```
Se a saída do comando foi semelhante a esta, com o seu nome de usuário ao invés do meu, é claro, estamos indo bem.

Para confirmar, imprima o help do Spack.
```console
[ramiro@zumbi ~]$ spack help
usage: spack [-hkV] [-e ENV] [--color {always,never,auto}] COMMAND ...

A flexible package manager that supports multiple versions,
configurations, platforms, and compilers.

Common spack commands:

build, install, and test packages:
  install               build and install packages
  uninstall             remove installed packages
  gc                    remove specs that are now no longer needed
  spec                  show what would be installed, given a spec

configuration:
  arch                  print architecture information about this machine
  compilers             list available compilers
  external              manage external packages in Spack configuration

environments:
  env                   manage environments
  view                  manipulate view directories in the filesystem

create packages:
  create                create a new package file
  edit                  open package files in ``$EDITOR``
  audit                 audit configuration files, packages, etc.

query packages:
  find                  list and search installed packages
  info                  get detailed information on a particular package
  list                  list and search available packages

user environment:
  load                  add package to the user environment
  module                generate/manage module files
  unload                remove package from the user environment

Options:

general:
  --color {always,never,auto}
                        when to colorize output (default: auto)
  -V, --version         show version number and exit
  -h, --help            show this help message and exit
  -k, --insecure        do not check ssl certificates when downloading

configuration and environments:
  -e ENV, --env ENV     run with an environment

More help:
  spack help --all       list all commands and options
  spack help <command>   help on a specific command
  spack help --spec      help on the package specification syntax
  spack docs             open https://spack.rtfd.io/ in a browser
```

## Pacotes disponíveis

O comando `spack list` mostra os pacotes disponíveis para instalação, mas antes de tentar imprimir a lista, vamos ter uma ideia de quantos pacotes estão disponíveis.

```console
[ramiro@zumbi ~]$ spack list | wc -l
remote: Enumerating objects: 20288, done.
remote: Counting objects: 100% (20288/20288), done.
remote: Compressing objects: 100% (10942/10942), done.
remote: Total 20288 (delta 1335), reused 14098 (delta 1165), pack-reused 0 (from 0)
8874
[ramiro@zumbi ~]$
```
Bom, basicamente, são 8900 pacotes. Não vou colocar a lista toda aqui. Neste caso, ajuda saber a priori o que você quer. Se nesta lista quase infinita você encontrar alguma coisa que você precisa, e ache que pode ser de interesse geral, fale conosco que, dependendo das circunstâncias, podemos instalá-la. Ao instalar via Spack como administrador do sistema, este pacote ficará automaticamente disponíveis para carregamento via o sistema de módulos do OpenHPC para todos os usuários do sistema, o que é muito conveniente.

## Instalação de pacote
Vamos supor que você  queira instalar o pacote [FreeFEM](https://freefem.org/) (que eu não sei se é bom, se resolve alguma coisa para alguém, foi apenas uma coisa que vi na lista com um nome bonito).

Eu tenho que saber, tipicamente através de uma busca no Google, que o FreeFEM está disponível no Spack, com o nome de pacote "freefem". Podemos verificar isto,

```console
[ramiro@zumbi ~]$ spack info freefem
AutotoolsPackage:   freefem

Description:
    FreeFEM is a popular 2D and 3D partial differential equations (PDE)
    solver. It allows you to easily implement your own physics modules using
    the provided FreeFEM language. FreeFEM offers a large list of finite
    elements, like the Lagrange, Taylor-Hood, etc., usable in the continuous
    and discontinuous Galerkin method framework.

Homepage: https://freefem.org

Preferred version:  
    4.16       https://github.com/FreeFem/FreeFem-sources/archive/refs/tags/v4.16.tar.gz

Safe versions:  
    develop    [git] https://github.com/FreeFem/FreeFem-sources.git on branch develop
    4.16       https://github.com/FreeFem/FreeFem-sources/archive/refs/tags/v4.16.tar.gz
    4.15       https://github.com/FreeFem/FreeFem-sources/archive/refs/tags/v4.15.tar.gz
    4.14       https://github.com/FreeFem/FreeFem-sources/archive/refs/tags/v4.14.tar.gz
    4.13       https://github.com/FreeFem/FreeFem-sources/archive/refs/tags/v4.13.tar.gz
    4.12       https://github.com/FreeFem/FreeFem-sources/archive/refs/tags/v4.12.tar.gz
    4.11       https://github.com/FreeFem/FreeFem-sources/archive/refs/tags/v4.11.tar.gz
    4.10       https://github.com/FreeFem/FreeFem-sources/archive/refs/tags/v4.10.tar.gz
    4.9        https://github.com/FreeFem/FreeFem-sources/archive/refs/tags/v4.9.tar.gz
    4.8        https://github.com/FreeFem/FreeFem-sources/archive/refs/tags/v4.8.tar.gz
    4.7-1      https://github.com/FreeFem/FreeFem-sources/archive/refs/tags/v4.7-1.tar.gz
    4.7        https://github.com/FreeFem/FreeFem-sources/archive/refs/tags/v4.7.tar.gz
    4.6        https://github.com/FreeFem/FreeFem-sources/archive/refs/tags/v4.6.tar.gz
    4.5        https://github.com/FreeFem/FreeFem-sources/archive/refs/tags/v4.5.tar.gz

Deprecated versions:  
    None

Variants:
    build_system [autotools]        autotools
        Build systems supported by the package

    mpi [false]                     false, false
        Activate MPI support

    petsc [false]                   false, false
        Compile with PETSc/SLEPc

    superlu [false]                  false, false
        Activate SuperLU support


Dependencies:
    autoconf         build

    automake         build

    bison            build

    c                build

    cxx              build

    flex             build

    fortran          build

    gmake            build
      when  build_system=autotools

    gnuconfig        build
      when  build_system=autotools target=ppc64le:

    gnuconfig        build
      when  build_system=autotools target=aarch64:

    gnuconfig        build
      when  build_system=autotools target=riscv64:

    lapack           build, link

    libtool          build

    m4               build

    mpi              build, link
      when +mpi

    slepc            build, link
      when +petsc


Licenses:
    LGPL-3.0-only
```

Nesta descrição temos uma lista de versões disponíveis. Se não escolhermos nenhuma específica, o Spack instalará a "preferida", tipicamente a mais recente. Há também uma lista de "variantes", que são opções de construção deste programa, e aqui o interessante é que podemos ter uma versão paralela usando MPI e usando o PetSci, que não são usados por default, e usando o SuperLU como solver, que é usado por padrão.

O Spack vai construir a partir da fonte todas as dependências deste pacote, incluindo o PetSc, SuperLU e MPI, e, inclusive, os compiladores que ele usa! Normalmente não queremos isto, queremos que ele use a versão do MPI do sistema, que, em princípio, está perfeitamente integrada e funciona corretamente com o Slurm.

Podemos configurar o Spack para usar algumas bibliotecas e pacotes do sistema. Tudo o que ele precisar e não encontrar automaticamente, será reconstruído a partir do código fonte.

### Configuração local

Você **realmente** não quer o Spack reconstrua as suas bibliotecas MPI e os seus compiladores, então temos que avisá-lo para não fazer isto.
Crie um arquivo no diretório "~/.spack" com o nome "packages.yaml", contendo 

```yaml
packages:
  mpi:
    externals:
    - spec: openmpi@5.0.7
      prefix: /opt/ohpc/pub/mpi/openmpi5-gnu14/5.0.7
      extra_attributes:
        environment:
          prepend_path:
            LD_LIBRARY_PATH: /opt/ohpc/pub/mpi/ucx-ohpc/1.18.0/lib
    buildable: false
```
Infelizmente, este arquivo terá que ser mantido "na mão", isto é, se algum dia você quiser usar outra versão do MPI, você tem que atualizá-lo manualmente.

Depois disto, verifique os módulos OpenHPC dos compiladores que você quer usar estão carregados.
```console
[ramiro@n01 ~]$ module list

Currently Loaded Modules:
  1) autotools   3) gnu15/15.2.0   5) ucx/1.18.0         7) openmpi5/5.0.7
  2) prun/2.2    4) hwloc/2.12.0   6) libfabric/1.18.0   8) ohpc
```

No meu caso, como eu quero usar o GCC-15, está tudo certo. Você então digita o comando
```console
[ramiro@n01 ~]$ spack compiler find
==> Added 2 new compilers to /home/ramiro/.spack/packages.yaml
    gcc@15.2.0  gcc@11.5.0
==> Compilers are defined in the following files:
    /home/ramiro/.spack/packages.yaml
```

Vamos entender o que aconteceu.
```console
[ramiro@zumbi ~]$ cat ~/.spack/packages.yaml 
packages:
  gcc:
    externals:
    - spec: gcc@15.2.0 languages:='c,c++,fortran'
      prefix: /opt/ohpc/pub/compiler/gcc/15.2.0
      extra_attributes:
        compilers:
          c: /opt/ohpc/pub/compiler/gcc/15.2.0/bin/gcc
          cxx: /opt/ohpc/pub/compiler/gcc/15.2.0/bin/g++
          fortran: /opt/ohpc/pub/compiler/gcc/15.2.0/bin/gfortran
    - spec: gcc@11.5.0 languages:='c,c++'
      prefix: /usr
      extra_attributes:
        compilers:
          c: /usr/bin/gcc
          cxx: /usr/bin/g++
  mpi:
    externals:
    - spec: openmpi@5.0.7
      prefix: /opt/ohpc/pub/mpi/openmpi5-gnu14/5.0.7
      extra_attributes:
        environment:
          prepend_path:
            LD_LIBRARY_PATH: /opt/ohpc/pub/mpi/ucx-ohpc/1.18.0/lib
    buildable: false
[ramiro@zumbi ~]$
```

O arquivo "~/.spack/packages.yaml" foi modificado pelo próprio Spack.
Notem que o Spack achou o GCC-15 instalado pelo OpenHPC, e  GCC-11, da instalação normal do sistema operacional. É muito preferível usar o GCC-15 do OpenHPC pela compatibilidade com as outras bibliotecas instaladas, e por ser mais moderno. Você pode preferir usar uma versão mais antiga, por compatibilidade com algum programa, carregue o módulo correspondente e repita o comando `spack compiler find`, ou simplesmente edite o arquivo "pacakges.yaml" diretamente usando o GCC-15 como padrão.

Aliás, se você quiser usar uma das bibliotecas científicas instaladas pelo OpenHPC no Spack, tem que configurá-la de forma análoga ao que fizemos com o MPI, caso contrário o Spack vai construí-la a partir do código fonte. Se for um pacote que já vem com o OpenHPC, isto é relativamente fácil. Precisamos editar o arquivo "~/.spack/packages.yaml", como mostrado a seguir. O arquivo abaixo está configurado para usar o Trilinos do OpenHPC.

```yaml
[ramiro@zumbi ~]$ cat ~/.spack/packages.yaml 
packages:
  gcc:
    externals:
    - spec: gcc@15.2.0 languages:='c,c++,fortran'
      prefix: /opt/ohpc/pub/compiler/gcc/15.2.0
      extra_attributes:
        compilers:
          c: /opt/ohpc/pub/compiler/gcc/15.2.0/bin/gcc
          cxx: /opt/ohpc/pub/compiler/gcc/15.2.0/bin/g++
          fortran: /opt/ohpc/pub/compiler/gcc/15.2.0/bin/gfortran
    - spec: gcc@11.5.0 languages:=c
      prefix: /usr
      extra_attributes:
        compilers:
          c: /usr/bin/gcc
  mpi:
    externals:
    - spec: openmpi@5.0.7
      prefix: /opt/ohpc/pub/mpi/openmpi5-gnu15/5.0.7
    buildable: false
  trilinos:
    externals:
    - spec: trilinos@16.1.0
      modules: 
        - trilinos/16.1.0
[ramiro@zumbi ~]$
```
Eu não *super* testei isto, então, qualquer correção é bem vinda. Caso a biblioteca que você deseja usar seja uma biblioteca do sistema operacional e  não esteja disponível como um módulo OpenHPC, também é possível usá-la, [especificando diretamente](https://spack.readthedocs.io/en/latest/packages_yaml.html) o diretório de instalação. Não é completamente óbvio que é melhor usar pacotes préinstalados do OpenHPC do que compilar o código fonte das dependências, já que o código distribuído pelo OpenHPC está compilado para um processador genérico, enquanto o compilado pelo OpenHPC, se você fizer tudo corretamente, será otimizado para os processadores que estão instalados nos nós computacionais.

### Ambientes 

A boa prática do Spack é criar ambientes para isolados para cada projeto, onde você pode manter as versões de cada biblioteca e programa que o seu projeto usa compatíveis entre si.
Vamos criar um ambiente  para o FreeFem.

```console
[ramiro@zumbi tmp]$ spack env create FreeFem
==> Created environment FreeFem in: /home/ramiro/.spack/environments/FreeFem
==> Activate with: spack env activate FreeFem
```
Note que o Spack recomenda que você ative o ambiente, para que os novos pacotes sejam instalados neste ambiente, e para que os pacotes instalados neste ambiente tornem-se acessíveis ao usuário. Não se assuste, isto pode demorar um pouco. Como a instalação de pacotes pode ser muito demorada, vamos fazer isto em um nó computacional.  Outra vantagem de fazer a instalação em um nó é que o Spack vai usar a arquitetura correta do processador automaticamente, você não vai precisar se preocupar em configurar isto.

Vamos alocar um shell interativo, como vimos [anteriormente](/slurm), com 8 processadores disponíveis, e imediatamente ativar o ambiente.


```console
[ramiro@zumbi ~]$ salloc --ntasks=1 --cpus-per-task=8 --time=12:00:00
salloc: Granted job allocation 2703
salloc: Nodes n01 are ready for job
[ramiro@n01 ~]$ spack env activate -p FreeFEM
[FreeFEM] [ramiro@n01 ~]$ 
```

O flag "-p" altera o prompt do shell e é muito útil para lembrar qual ambiente está ativo. A partir de agora, tudo o que o fizermos é específico a este ambiente. Vamos ver se já há algo instalado neste ambiente.

```console
[FreeFEM] [ramiro@n01 ~]$ spack find
==> In environment FreeFEM (no root specs)
==> 0 installed packages
==> 0 concretized packages to be installed (show with `spack find -c`)
[FreeFEM] [ramiro@n01 ~]$
```
Não tem nada ainda, o que normal em um ambiente recém criado.


### Instalação em ambientes

A instalação de pacotes em um ambiente ativado é um processo de várias etapas. Primeiro é necessário informar ao Spack qual pacote você pretende instalar, com as variantes desejadas.
Não é estritamente necessário, mas é útil tentar "concretizar" o pacote antes de instalá-lo, assim o Spack vai verificar se e como todas as dependências podem ser resolvidas.

Isto pode demorar um bom tempo. Por este motivo, e para que a arquitetura do processador seja escolhida corretamente (é possível especificar isto manualmente), a partir daqui vamos trabalhar em um shell em um nó computacional. Temos que ativar todo o ambiente do Spack novamente. Vamos alocar 8 processadores porque o Spack pode compilar mais de um arquivo por simultaneamente.

Vamos inicialmente informar ao Spack o que queremos instalar. Não é necessário ativar as variantes petsc e mpi, pois são ativadas por default, fiz isto para apenas para exemplificar.

```console
[FreeFEM] [ramiro@n01 ~]$ spack add freefem +petsc +mpi +superlu
==> Adding freefem+mpi+petsc+superlu to environment FreeFEM
[FreeFEM] [ramiro@n01 ~]$
```

Não é estritamente necessário, mas é boa prática "concretizar" o ambiente antes de tentar a compilação. Isto vai coletar e verificar se todas as dependências do pacote estão disponíveis e compatíveis com os outros pacotes já instalados no sistema. Isto pode demorar um bom tempo, dependendo da complexidade da instalação! Não é inconcebível demorar dezenas de minutos para um pacote complexo em um ambiente com muitos pacotes. No meu caso, para este pacote específico, levou por volta de 1 minuto. Vamos usar todos os processadores alocados para este shell com opção "-j 8".

```console
[FreeFEM] [ramiro@n01 ~]$ spack concretize -j 8
==> Installing "clingo-bootstrap@=spack~apps~docs+ipo+optimized+python+static_libstdcpp build_system=cmake build_type=Release commit=2a025667090d71b2c9dce60fe924feb6bde8f667 generator=make patches:=bebb819,ec99431 platform=linux os=centos7 target=x86_64" from a buildcache
==> Concretized 1 spec:
 -   f4r7i6t  freefem@4.16+mpi+petsc+superlu build_system=autotools platform=linux os=rocky9 target=zen3 %c,cxx,fortran=gcc@15.2.0
 -   2u7syu2      ^autoconf@2.72 build_system=autotools platform=linux os=rocky9 target=zen3 
 -   f2b5bgq          ^perl@5.42.0+cpanm+opcode+open+shared+threads build_system=generic platform=linux os=rocky9 target=zen3 %c=gcc@15.2.0
 -   wnhj6qa              ^berkeley-db@18.1.40+cxx~docs+stl build_system=autotools patches:=26090f4,b231fcc platform=linux os=rocky9 target=zen3 %c,cxx=gcc@15.2.0
 -   fvhjct3              ^bzip2@1.0.8~debug~pic+shared build_system=generic platform=linux os=rocky9 target=zen3 %c=gcc@15.2.0
 -   26ifjyj              ^gdbm@1.26 build_system=autotools platform=linux os=rocky9 target=zen3 %c=gcc@15.2.0
 -   44rog2j              ^less@692 build_system=autotools platform=linux os=rocky9 target=zen3 %c=gcc@15.2.0
 -   pdqe63l              ^zlib-ng@2.3.3+compat+new_strategies+opt+pic+shared build_system=autotools platform=linux os=rocky9 target=zen3 %c,cxx=gcc@15.2.0
 -   solz4ff      ^automake@1.18.1 build_system=autotools platform=linux os=rocky9 target=zen3 %c=gcc@15.2.0
 -   wu6tt65      ^bison@3.8.2~color build_system=autotools platform=linux os=rocky9 target=zen3 %c,cxx=gcc@15.2.0
 -   3kjfs7d          ^diffutils@3.12 build_system=autotools platform=linux os=rocky9 target=zen3 %c=gcc@15.2.0
 -   64gzdoo              ^libiconv@1.18 build_system=autotools libs:=shared,static platform=linux os=rocky9 target=zen3 %c=gcc@15.2.0
 -   wth6fsz      ^compiler-wrapper@1.1.0 build_system=generic platform=linux os=rocky9 target=zen3 
 -   fknlvly      ^flex@2.6.3+lex~nls build_system=autotools platform=linux os=rocky9 target=zen3 %c,cxx=gcc@15.2.0
 -   5ny2zkj          ^findutils@4.10.0 build_system=autotools patches:=440b954 platform=linux os=rocky9 target=zen3 %c=gcc@15.2.0
 -   hiyaobz              ^gettext@1.0+bzip2+curses+git~libunistring+libxml2+pic+shared+tar+xz build_system=autotools platform=linux os=rocky9 target=zen3 %c,cxx=gcc@15.2.0
 -   5bjlacd                  ^libxml2@2.15.3+pic~python+shared build_system=autotools platform=linux os=rocky9 target=zen3 %c=gcc@15.2.0
 -   jednx4g                  ^tar@1.35 build_system=autotools zip=pigz platform=linux os=rocky9 target=zen3 %c=gcc@15.2.0
 -   vpvuut6                      ^pigz@2.8 build_system=makefile platform=linux os=rocky9 target=zen3 %c=gcc@15.2.0
[e]  bmdqosz      ^gcc@15.2.0+binutils+bootstrap~graphite+libsanitizer~mold~nvptx~piclibs~profiled~strip build_system=autotools build_type=RelWithDebInfo languages:='c,c++,fortran' platform=linux os=rocky9 target=x86_64 
 -   fhqy3p2      ^gcc-runtime@15.2.0 build_system=generic platform=linux os=rocky9 target=zen3 
[e]  ppxsf4c      ^glibc@2.34 build_system=autotools platform=linux os=rocky9 target=x86_64 
 -   wbjrptm      ^gmake@4.4.1~guile build_system=generic platform=linux os=rocky9 target=zen3 %c=gcc@15.2.0
 -   tnrgrge      ^libtool@2.5.4 build_system=autotools platform=linux os=rocky9 target=zen3 %c=gcc@15.2.0
 -   y6pfyci          ^file@5.46+static build_system=autotools platform=linux os=rocky9 target=zen3 %c=gcc@15.2.0
 -   q5zjdkb              ^xz@5.8.3~pic build_system=autotools libs:=shared,static platform=linux os=rocky9 target=zen3 %c=gcc@15.2.0
 -   7z43jqo              ^zstd@1.5.7+programs build_system=makefile compression:=none libs:=shared,static platform=linux os=rocky9 target=zen3 %c,cxx=gcc@15.2.0
 -   zqnjuiv      ^m4@1.4.21+sigsegv build_system=autotools platform=linux os=rocky9 target=zen3 %c,cxx=gcc@15.2.0
 -   o5sdlzh          ^libsigsegv@2.15 build_system=autotools platform=linux os=rocky9 target=zen3 %c=gcc@15.2.0
 -   vbufjvl      ^openblas@0.3.33~bignuma~consistent_fpcsr+dynamic_dispatch+fortran~ilp64+locking+pic+shared~static build_system=makefile patches:=723ddc1 symbol_suffix=none threads=none platform=linux os=rocky9 target=zen3 %c,cxx,fortran=gcc@15.2.0
[e]  ftgniaa      ^openmpi@5.0.7+atomics~cuda~debug+fortran~gpfs~internal-hwloc~internal-libevent~internal-pmix~ipv6~java~lustre~memchecker~openshmem~rocm+romio+rsh~static~two_level_namespace+vt+wrapper-rpath build_system=autotools fabrics:=none romio-filesystem:=none schedulers:=none platform=linux os=rocky9 target=x86_64 
 -   gcc4i33      ^slepc@3.25.1~arpack~blopex~cuda~hpddm~rocm build_system=generic platform=linux os=rocky9 target=zen3 %c,cxx,fortran=gcc@15.2.0
 -   iaaywlx          ^petsc@3.25.1~X~batch~cgns~complex~cuda~debug+double+examples~exodusii~fftw+fortran+fortran-bindings~giflib~hdf5~hpddm~hwloc~hypre~int64~jpeg~knl~kokkos~libpng~libyaml~memkind~metis~mkl-pardiso~ml~mmg~moab~mpfr+mpi~mumps~openmp~p4est~parmmg~ptscotch~random123~rocm~saws~scalapack+shared~strumpack~suite-sparse~superlu-dist~sycl~tetgen~valgrind~zoltan build_system=generic clanguage=C memalign=none platform=linux os=rocky9 target=zen3 %c,cxx,fortran=gcc@15.2.0
 -   qgvvv2c          ^python@3.14.5+bz2+ctypes+dbm~debug~freethreading+libxml2+lzma~optimizations+pic+pyexpat+pythoncmd+readline+shared+sqlite3+ssl~static~tests~tkinter+uuid+zlib+zstd build_system=generic platform=linux os=rocky9 target=zen3 %c,cxx=gcc@15.2.0
 -   xwdun4b              ^expat@2.8.1+libbsd build_system=autotools platform=linux os=rocky9 target=zen3 %c,cxx=gcc@15.2.0
 -   6tqn2th                  ^libbsd@0.12.2 build_system=autotools platform=linux os=rocky9 target=zen3 %c=gcc@15.2.0
 -   4qqlvnr                      ^libmd@1.1.0 build_system=autotools platform=linux os=rocky9 target=zen3 %c=gcc@15.2.0
 -   4bkbny4              ^libffi@3.5.2 build_system=autotools platform=linux os=rocky9 target=zen3 %c,cxx=gcc@15.2.0
 -   y4nxhom              ^ncurses@6.6~symlinks+termlib abi=none build_system=autotools patches:=7a351bc platform=linux os=rocky9 target=zen3 %c,cxx=gcc@15.2.0
 -   4fjzw2k              ^openssl@3.6.1~docs+shared build_system=generic certs=mozilla platform=linux os=rocky9 target=zen3 %c,cxx=gcc@15.2.0
 -   wuwi4h5                  ^ca-certificates-mozilla@2026-03-19 build_system=generic platform=linux os=rocky9 target=zen3 
 -   63fuxmq              ^pkgconf@2.5.1 build_system=autotools platform=linux os=rocky9 target=zen3 %c=gcc@15.2.0
 -   sikljpw              ^readline@8.3 build_system=autotools patches:=21f0a03 platform=linux os=rocky9 target=zen3 %c=gcc@15.2.0
 -   sochmu5              ^sqlite@3.53.1+column_metadata+fts+rtree build_system=autotools platform=linux os=rocky9 target=zen3 %c=gcc@15.2.0
 -   mrpn4l5              ^util-linux-uuid@2.41 build_system=autotools platform=linux os=rocky9 target=zen3 %c=gcc@15.2.0
```
A sua saída pode ser completamente diferente, dependendo do que você já tem instalado, da versão do Spack, e muitos outros fatores. Não se preocupe com isto.
Esta saída  mostra tudo o que vai ser compilado e instalado, a partir do código fonte, com as suas versões, variantes e opções. Parece um novo sistema operacional inteiro, e quase é. No entanto, quando você for instalar outros pacotes, mesmo em outros ambientes, o Spack reaproveita dependências já instaladas e não as instala novamente. Por isto que pedi 12 horas para este shell, e 8 processadores. Parece exagerado, mas você nunca sabe.

Ao instalar o pacote, o Spack vai compilar toda esta lista de dependências. Só para ter certeza do que vai acontecer, você pode fazer

```console
[FreeFEM] [ramiro@n01 ~]$ spack find -c
==> In environment FreeFEM (1 root spec)
 -  freefem+mpi+petsc+superlu

-- linux-rocky9-x86_64 / no compilers ---------------------------
[e]  gcc@15.2.0  [e]  glibc@2.34  [e]  openmpi@5.0.7

-- linux-rocky9-zen3 / %c,cxx,fortran=gcc@15.2.0 ----------------
 -   freefem@4.16   -   openblas@0.3.33   -   petsc@3.25.1   -   slepc@3.25.1

-- linux-rocky9-zen3 / %c,cxx=gcc@15.2.0 ------------------------
 -   berkeley-db@18.1.40   -   flex@2.6.3     -   m4@1.4.21       -   python@3.14.5
 -   bison@3.8.2           -   gettext@1.0    -   ncurses@6.6     -   zlib-ng@2.3.3
 -   expat@2.8.1           -   libffi@3.5.2   -   openssl@3.6.1   -   zstd@1.5.7

-- linux-rocky9-zen3 / %c=gcc@15.2.0 ----------------------------
 -   automake@1.18.1    -   gdbm@1.26       -   libmd@1.1.0       -   pigz@2.8        -   util-linux-uuid@2.41
 -   bzip2@1.0.8        -   gmake@4.4.1     -   libsigsegv@2.15   -   pkgconf@2.5.1   -   xz@5.8.3
 -   diffutils@3.12     -   less@692        -   libtool@2.5.4     -   readline@8.3
 -   file@5.46          -   libbsd@0.12.2   -   libxml2@2.15.3    -   sqlite@3.53.1
 -   findutils@4.10.0   -   libiconv@1.18   -   perl@5.42.0       -   tar@1.35

-- linux-rocky9-zen3 / no compilers -----------------------------
 -   autoconf@2.72   -   ca-certificates-mozilla@2026-03-19   -   compiler-wrapper@1.1.0   -   gcc-runtime@15.2.0
==> 0 installed packages
==> 45 concretized packages to be installed
[FreeFEM] [ramiro@n01 ~]$
```
o que é a mesma informação que vimos na saída do comando para concretizar a instalação, só que mais organizada. Podemos agora tentar compilar tudo isto, mas antes, é bom avisar para o compilador  que ele pode usar como diretório temporário o SSD. Isto vai fazer as compilações rodarem **muito** mais rapidamente. Também vamos usar todos os processadores que alocamos. A sua tela do terminal vai sendo atualizada conforme a instalação progride, vou colocar aqui apenas uma captura após o término da instalação. Não é necessário dizer o que instalar, o Spack vai tentar instalar tudo o que foi "concretizado".


```console
[FreeFem] [ramiro@n01 ~]$ export TMP=/scratch/${SLURM_JOB_USER}.${SLURM_JOB_ID}
[FreeFEM] [ramiro@n01 ~]$ spack install -j8 
#
# Many many many deleted lines and a long long time later...
# 
[+] luqoqtx freefem@4.16 /data/ramiro/spack-local/opt/spack/linux-zen3/freefem-4.16-luqoqtx7ovtn434gj4lsnyqjwwdtjnrw (11m12s)
==> Updating view at /data/ramiro/spack-local/var/spack/environments/FreeFEM/.spack-env/view
[FreeFEM] [ramiro@n02 ~]$
```
Os binários e pacotes deste sistema estão disponíveis automaticamente quando o ambiente estiver ativo, e também são acessíveis de fora do ambiente. Apenas para verificar que funcionou, 

```console
[FreeFEM] [ramiro@n02 ~]$ FreeFem++ -?
FreeFem++ - version 4.16 (Tue Jun  2 17:06:36 -03 2026 - git no git) 64bits
License: LGPL 3+ (https://www.gnu.org/licenses/lgpl-3.0.en.html)
Usage: FreeFem++ [FreeFEM arguments] filename [script arguments]
FreeFEM arguments:
	-f:     [filename]  script file name
	-v:     [verbosity] level of FreeFEM output (0 - 1000000)
	-nw:                no graphics
	-wg:                with graphics
	-ne:                no edp script output
	-cd:                change directory to script directory
	-cdtmp:             change directory to /tmp
	-jc:                just compile
	-ns:                same as -ne
	-md:                MarkDown script
	-nowait:            do not wait graphics at the end
	-nc:                without console (MS Windows only)
	-log:               with console (MS Windows only)
	-wait:              wait graphics at the end
	-fglut: [filename]  redirect graphics in file
	-glut:  [command]   use custom glut
	-gff:   [command]   use custom glut (with space quoting)
	-check_plugin [plugin1,plugin2,...]        just try if this plugins are correct
	-?:                 show help

without default ffglut: ffglut

FreeFEM website: https://freefem.org/
FreeFEM documentation: https://doc.freefem.org/
FreeFEM forum: https://community.freefem.org/
FreeFEM modules: https://modules.freefem.org/

Please cite us in your research papers and add a link to FreeFEM on your personal website
@article{FreeFEM,
	AUTHOR = {Hecht, F.},
	TITLE = {New development in FreeFem++},
	JOURNAL = {J. Numer. Math.},
	FJOURNAL = {Journal of Numerical Mathematics},
	VOLUME = {20}, YEAR = {2012},
	NUMBER = {3-4}, PAGES = {251--265},
	ISSN = {1570-2820},
	MRCLASS = {65Y15},
	MRNUMBER = {3043640},
	URL = {https://freefem.org/}
}
```
Mais um teste simples,
```console
[FreeFEM] [ramiro@n02 ~]$ cp $SPACK_ENV/.spack-env/view/share/FreeFEM/4.16/examples/tutorial/Laplace.edp .
[FreeFEM] [ramiro@n02 ~]$ FreeFem++ -nw Laplace.edp 
-- FreeFem++ v4.16 (Tue Jun  2 17:06:36 -03 2026 - git no git)
   file : Laplace.edp
 Load: lg_fem  no UMFPACK -> replace by LU or GMRES  lg_mesh lg_mesh3 
    1 :  mesh Th=square(10,10);
    2 :  fespace Vh(Th,P1);     // P1 FE space
    3 :  Vh uh,vh;              // unkown and test function. 
    4 :  func f=1;                 //  right hand side function 
    5 :  func g=0;                 //  boundary condition function
    6 :  
    7 :  problem laplace(uh,vh,solver=GMRES,tgv=1e5) =                    //  definion of  the problem 
    8 :     int2d(Th)( dx(uh)*dx(vh) + dy(uh)*dy(vh) ) //  bilinear form
    9 :   - int2d(Th)( f*vh )                          //  linear form
   10 :   + on(1,2,3,4,uh=g) ;                      //  boundary condition form
   11 : 
   12 :   laplace; // solve the problem plot(uh); // to see the result
   13 :   plot(uh,ps="Laplace.eps",value=false);
   14 :  sizestack + 1024 =1736  ( 712 )

  -- Square mesh : nb vertices  =121 ,  nb triangles = 200 ,  nb boundary edges 40 rmdup= 0
  **  fgmres has converged in 13 iterations The relative residual is 5.54823e-10 Cl: 0.09
  -- Solve : 
          min 1.28117e-12  max 0.0730987
times: compile 0.002488s, execution 0.003377s,  mpirank:0
 CodeAlloc : nb ptr  3962,  size :534816 mpirank: 0
Ok: Normal End
[FreeFEM] [ramiro@n02 ~]$
```
Vamos confiar que está tudo certo. 

Pensando bem, este talvez não tenha sido a melhor escolha para um exemplo do Spack, por que é um sistema enorme que instala um milhão de coisas. Caso você reproduza este exemplo, mas não deseje usar o FreeFEM, pelo menos por enquanto, lembre-se de removê-lo, bem como suas dependências não mais necessárias, com 

```console
[FreeFEM] [ramiro@n02 ~]$spack deactivate FreeFEM
[ramiro@n02 ~]$spack env remove FreeFEM
[ramiro@n02 ~]$spack gc
```

É raro, mas pode acontecer, que alguma compilação falhe. Você pode tentar eliminar alguma variante problemática, ou tentar um outro compilador, ou tentar resolver o problema modificando o código fonte do programa. Desnecessário dizer que esta última opção requer um certo conhecimento.
