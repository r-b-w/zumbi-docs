---
date: '2026-03-24T18:43:16-03:00'
draft: true
title: 'Spack'
weight: '10'
cascade:
    type: docs
---

O [Spack](https://spack.io/https://spack.io/) é um sistema de gerenciamento de pacotes e ambientes para programação científica. É um sistema *extremamente sofisticado*. É uma das maravilhas da natureza. Não é uma coisa que vamos explicar em algumas poucas linhas.  
Nesta seção vamos mostrar um exemplo simples do uso do Spack para instalar um pacote científico.


O Spack foi desenvolvido, principalmente, para ser usado por um pesquisador ou grupo, que cria um ambiente específico para um projeto, onde todas as dependências são mantidas automaticamente e compatíveis entre si, muito semelhante, e não por coincidência, aos [ambientes virtuais](https://docs.python.org/pt-br/3/library/venv.html) usados na linguagem Python . 

A ideia é que o grupo instale o Spack em um diretório compartilhado para o grupo e instale os programas e bibliotecas que este projeto precisa usando o próprio Spack. Como o spack, por default, instala os programas a partir do código fonte, e, por default, compila  os programas para  a arquitetura do processador no qual está rodando, com opções de otimização agressivas, (isto é configurável também), os programas executáveis tendem a ser mais rápidos do que os mesmos programas instalados de uma distribuição Linux geral, ou via OpenHPC. Isto também reforça a necessidade de alocar um shell interativo em um nó computacional e não fazê-lo no nó de login.

## Módulo
O Spack é instalado junto com o OpenHPC, e um usuário normal pode usá-lo diretamente, mas com sutilezas. 


```console
[ramiro@zumbi ~]$ module load spack
[ramiro@zumbi ~]$ spack --help
usage: spack [-hkV] [--color {always,never,auto}] COMMAND ...

A flexible package manager that supports multiple versions,
configurations, platforms, and compilers.

These are common spack commands:

query packages:
  list                  list and search available packages
  info                  get detailed information on a particular package
  find                  list and search installed packages

build packages:
  install               build and install packages
  uninstall             remove installed packages
  gc                    remove specs that are now no longer needed
  spec                  show what would be installed, given a spec

configuration:
  external              manage external packages in Spack configuration

environments:
  env                   manage virtual environments
  view                  project packages to a compact naming scheme on the filesystem

create packages:
  create                create a new package file
  edit                  open package files in $EDITOR

system:
  arch                  print architecture information about this machine
  audit                 audit configuration files, packages, etc.
  compilers             list available compilers

user environment:
  load                  add package to the user environment
  module                generate/manage module files
  unload                remove package from the user environment

optional arguments:
  --color {always,never,auto}
                        when to colorize output (default: auto)
  -V, --version         show version number and exit
  -h, --help            show this help message and exit
  -k, --insecure        do not check ssl certificates when downloading

more help:
  spack help --all       list all commands and options
  spack help <command>   help on a specific command
  spack help --spec      help on the package specification syntax
  spack docs             open https://spack.rtfd.io/ in a browser
[ramiro@zumbi ~]$ spack help
usage: spack [-hkV] [--color {always,never,auto}] COMMAND ...

A flexible package manager that supports multiple versions,
configurations, platforms, and compilers.

These are common spack commands:

query packages:
  list                  list and search available packages
  info                  get detailed information on a particular package
  find                  list and search installed packages

build packages:
  install               build and install packages
  uninstall             remove installed packages
  gc                    remove specs that are now no longer needed
  spec                  show what would be installed, given a spec

configuration:
  external              manage external packages in Spack configuration

environments:
  env                   manage virtual environments
  view                  project packages to a compact naming scheme on the filesystem

create packages:
  create                create a new package file
  edit                  open package files in $EDITOR

system:
  arch                  print architecture information about this machine
  audit                 audit configuration files, packages, etc.
  compilers             list available compilers

user environment:
  load                  add package to the user environment
  module                generate/manage module files
  unload                remove package from the user environment

optional arguments:
  --color {always,never,auto}
                        when to colorize output (default: auto)
  -V, --version         show version number and exit
  -h, --help            show this help message and exit
  -k, --insecure        do not check ssl certificates when downloading

more help:
  spack help --all       list all commands and options
  spack help <command>   help on a specific command
  spack help --spec      help on the package specification syntax
  spack docs             open https://spack.rtfd.io/ in a browser
[ramiro@zumbi ~]$
```

O comando `spack list` mostra os pacotes disponíveis para instalação, mas antes de tentar imprimir a list, vamos ver quantos pacotes estão disponíveis.
```console
[ramiro@zumbi ~]$ spack list | wc -l
8499
[ramiro@zumbi ~]$ 
```
Bom, basicamente, são 8500 pacotes. Não vou colocar a lista toda aqui. Neste caso, ajuda saber a priori o que você quer. Se nesta lista quase infinita você encontrar alguma coisa que você precisa, e ache que pode ser de interesse geral, fale conosco que, dependendo das circunstâncias, podemos instalá-la. Ao instalar via Spack como administrador do sistema, este pacote ficará automaticamente disponíveis para carregamento via o sistema de módulos do OpenHPC, o que é muito conveniente.

## Instalação de pacote
Vamos supor que você  queira instalar o pacote [FreeFEM](https://freefem.org/) (que eu não sei se é bom, se resolve alguma coisa para alguém, foi apenas uma coisa interessante que vi na lista). Eu tenho que saber, tipicamente através de uma busca no Google, que o FreeFEM está disponível no Spack, com o nome de pacote "freefem". Podemos verificar isto,

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
    4.14     https://github.com/FreeFem/FreeFem-sources/archive/refs/tags/v4.14.tar.gz

Safe versions:  
    4.14     https://github.com/FreeFem/FreeFem-sources/archive/refs/tags/v4.14.tar.gz
    4.13     https://github.com/FreeFem/FreeFem-sources/archive/refs/tags/v4.13.tar.gz
    4.12     https://github.com/FreeFem/FreeFem-sources/archive/refs/tags/v4.12.tar.gz
    4.11     https://github.com/FreeFem/FreeFem-sources/archive/refs/tags/v4.11.tar.gz
    4.10     https://github.com/FreeFem/FreeFem-sources/archive/refs/tags/v4.10.tar.gz
    4.9      https://github.com/FreeFem/FreeFem-sources/archive/refs/tags/v4.9.tar.gz
    4.8      https://github.com/FreeFem/FreeFem-sources/archive/refs/tags/v4.8.tar.gz
    4.7-1    https://github.com/FreeFem/FreeFem-sources/archive/refs/tags/v4.7-1.tar.gz
    4.7      https://github.com/FreeFem/FreeFem-sources/archive/refs/tags/v4.7.tar.gz
    4.6      https://github.com/FreeFem/FreeFem-sources/archive/refs/tags/v4.6.tar.gz
    4.5      https://github.com/FreeFem/FreeFem-sources/archive/refs/tags/v4.5.tar.gz

Deprecated versions:  
    None

Variants:
    build_system [autotools]        autotools
        Build systems supported by the package
    mpi [false]                     false, true
        Activate MPI support
    petsc [false]                   false, true
        Compile with PETSc/SLEPc

Build Dependencies:
    autoconf  automake  bison  c  cxx  flex  fortran  gmake  gnuconfig  libtool  m4  mpi  netlib-lapack  slepc

Link Dependencies:
    mpi  netlib-lapack  slepc

Run Dependencies:
    None

Licenses: 
    None
```

Nesta descrição temos uma lista de versões disponíveis. Se não escolhermos nenhuma específica, o Spack instalará a "preferida", tipicamente a mais recente. Há também uma lista de "variantes", que são opções de construção deste programa, e aqui o interessante é que podemos ter uma versão paralela usando MPI e usando o PetSc.

O Spack vai construir a partir da fonte todas as dependências deste pacote, incluindo o PetSc e o MPI! Normalmente não queremos isto, queremos que ele use a versão do MPI do sistema, que, em princípio, está perfeitamente integrada e funciona corretamente com o Slurm.
Podemos então configurar o Spack para usar algumas bibliotecas do sistema. Tudo o que ele precisar e não encontrar automaticamente, será reconstruído a partir do código fonte.

### Configuração local
Se você tentar instalar um pacote via Spack sem nenhuma outra configuração, não vai funcionar, já que ele vai tentar escrever em diretórios do sistema nos quais você não tem permissão. Você precisa então configurá-lo para usar os seus próprios diretórios.

Caso não exista, crie um diretório de configuração, chamado ".spack", no seu diretório raiz. Este é um diretório invisível, só vai aparecer em uma listagem se você usar `ls -a`.
```console
[ramiro@zumbi ~]$ mkdir ~/.spack
[ramiro@zumbi ~]$ 
```
Dentro do diretório "~/.spack", crie o arquivo "config.yaml", com o seguinte conteúdo
```yaml
config:
  environments_root: ~/.spack/environments
  install_tree:
    root: ~/data/my-spack-software
```
Obviamente, o nome "my-spack-software" pode ser qualquer coisa, mas lembre-se de criar este diretório na área de storage grande!

Para garantir que o Spack vá usar o seu compilador e o seu MPI, primeiro certifique-se que os módulos "gnu14" "openmpi5" estejam carregados, e crie um arquivo no diretório "~/.spack" com o nome "packages.yaml", contendo 

```yaml
packages:
  gcc:
    externals:
    - spec: gcc@14.2.0 languages:='c,c++,fortran'
      prefix: /opt/ohpc/pub/compiler/gcc/14.2.0
      extra_attributes:
        compilers:
          c: /opt/ohpc/pub/compiler/gcc/14.2.0/bin/gcc
          cxx: /opt/ohpc/pub/compiler/gcc/14.2.0/bin/g++
          fortran: /opt/ohpc/pub/compiler/gcc/14.2.0/bin/gfortran
  mpi:
    externals:
    - spec: openmpi@5.0.7
      prefix: /opt/ohpc/pub/mpi/openmpi5-gnu14/5.0.7
    buildable: false
```

Não é estritamente necessário, mas você pode fazer com que a sua instalação local do Spack use pacotes já instalados no Spack do sistema, pare não ter que recompilá-los, caso existam. Basta criar um arquivo "upstreams.yaml", no mesmo diretório, com o conteúdo
```yaml
upstreams:
  system-spack:
    install_tree: /opt/ohpc/pub/apps/spack/local
```

### Ambientes 
Conforme dissemos no início desta seção, a boa prática do Spack é criar ambientes para cada projeto. Para que os ambientes funcionem corretamente no Spack, você precisa configurar adequadamente o seu shell, adicionando a linha a seguir ao final do seu arquivo ".bashrc". Você vai ter que sair do Zumbi e voltar para que esta modificação tenha efeito.
```bash
. /opt/ohpc/pub/apps/spack/1.0.2/share/spack/setup-env.sh
```

Vamos criar um ambiente  para o FreeFem, e vamos imediatamente ativá-lo, o que é necessário para que os pacotes sejam instalados neste ambiente. Não se assuste, isto pode demorar um pouco.

```console
[ramiro@zumbi tmp]$ spack env create FreeFem
==> Created environment FreeFem in: /home/ramiro/.spack/environments/FreeFem
==> Activate with: spack env activate FreeFem
[ramiro@zumbi tmp]$ spack env activate -p FreeFem
[FreeFem] [ramiro@zumbi tmp]$ 
```
O flag "-p" altera o prompt do shell e é muito útil para lembrar qual ambiente está ativo.

### Instalação
A instalação de pacotes em um ambiente ativado é um processo de várias etapas. Primeiro é necessário adicionar o pacote ao ambiente, com as variantes desejadas.


```console
[FreeFem] [ramiro@zumbi ~]$ spack add freefem +mpi +petsc
==> Adding freefem+mpi+petsc to environment FreeFem
[FreeFem] [ramiro@zumbi ~]$ 
```
Não é estritamente necessário, mas é útil tentar "concretizar" o pacote antes de instalá-lo, assim o Spack vai verificar se todas as dependências podem ser resolvidas. Isto pode demorar um bom tempo. Por este motivo, e para que a arquitetura do processador seja escolhida corretamente (é possível especificar isto manualmente), a partir daqui vamos trabalhar em um shell em um nó computacional. Temos que ativar todo o ambiente do Spack novamente.


```console
[FreeFem] [ramiro@zumbi ~]$ salloc --ntasks=1 --cpus-per-task=8 --time=12:00:00
salloc: Granted job allocation 2664
salloc: Nodes n01 are ready for job
$[ramiro@n01 ~]$ module load spack
[ramiro@n01 ~]$ spack activate -p FreeFem
==> Error: activate is not a recognized Spack command or extension command; check with `spack commands`.
[ramiro@n01 ~]$ spack env activate -p FreeFem
[FreeFem] [ramiro@n01 ~]$ 
[FreeFem] [ramiro@n01 ~]$ spack concretize 
==> Concretized 1 spec:
 -   fvs44ra  freefem@4.14+mpi+petsc build_system=autotools arch=linux-rocky9-zen3 %c,cxx,fortran=gcc@14.2.0
 -   lmjykd6      ^autoconf@2.72 build_system=autotools arch=linux-rocky9-zen3 
 -   5bbbq5t          ^perl@5.40.0+cpanm+opcode+open+shared+threads build_system=generic arch=linux-rocky9-zen3 %c=gcc@14.2.0
 -   axp3mxv              ^berkeley-db@18.1.40+cxx~docs+stl build_system=autotools patches:=26090f4,b231fcc arch=linux-rocky9-zen3 %c,cxx=gcc@14.2.0
 -   nub2zpe              ^bzip2@1.0.8~debug~pic+shared build_system=generic arch=linux-rocky9-zen3 %c=gcc@14.2.0
 -   c6hokwu              ^gdbm@1.23 build_system=autotools arch=linux-rocky9-zen3 %c=gcc@14.2.0
 -   qgbzmzl              ^zlib-ng@2.2.4+compat+new_strategies+opt+pic+shared build_system=autotools arch=linux-rocky9-zen3 %c,cxx=gcc@14.2.0
 -   eptgqfc      ^automake@1.16.5 build_system=autotools arch=linux-rocky9-zen3 %c=gcc@14.2.0
 -   2xsodrv      ^bison@3.8.2~color build_system=autotools arch=linux-rocky9-zen3 %c,cxx=gcc@14.2.0
 -   a5sctfc          ^diffutils@3.10 build_system=autotools arch=linux-rocky9-zen3 %c=gcc@14.2.0
 -   qhoazmp              ^libiconv@1.18 build_system=autotools libs:=shared,static arch=linux-rocky9-zen3 %c=gcc@14.2.0
 -   fnp3ijg      ^compiler-wrapper@1.0 build_system=generic arch=linux-rocky9-zen3 
 -   dun2eas      ^flex@2.6.3+lex~nls build_system=autotools arch=linux-rocky9-zen3 %c,cxx=gcc@14.2.0
 -   qgk7s6t          ^findutils@4.10.0 build_system=autotools patches:=440b954 arch=linux-rocky9-zen3 %c=gcc@14.2.0
 -   cxntos2              ^gettext@0.23.1+bzip2+curses+git~libunistring+libxml2+pic+shared+tar+xz build_system=autotools arch=linux-rocky9-zen3 %c,cxx=gcc@14.2.0
 -   i7v4e3f                  ^libxml2@2.13.5~http+pic~python+shared build_system=autotools arch=linux-rocky9-zen3 %c=gcc@14.2.0
 -   lhvglxp                  ^tar@1.35 build_system=autotools zip=pigz arch=linux-rocky9-zen3 %c=gcc@14.2.0
 -   x57s3yc                      ^pigz@2.8 build_system=makefile arch=linux-rocky9-zen3 %c=gcc@14.2.0
 -   6nwiq73                      ^zstd@1.5.7+programs build_system=makefile compression:=none libs:=shared,static arch=linux-rocky9-zen3 %c,cxx=gcc@14.2.0
[e]  pr6p3ld      ^gcc@14.2.0~binutils+bootstrap~graphite~mold~nvptx~piclibs~profiled~strip build_system=autotools build_type=RelWithDebInfo languages:='c,c++,fortran' arch=linux-rocky9-zen3 
 -   ftgveik      ^gcc-runtime@14.2.0 build_system=generic arch=linux-rocky9-zen3 
[e]  7cjviwp      ^glibc@2.34 build_system=autotools arch=linux-rocky9-zen3 
 -   lm6qwjm      ^gmake@4.4.1~guile build_system=generic arch=linux-rocky9-zen3 %c=gcc@14.2.0
 -   yfhke4c      ^libtool@2.4.7 build_system=autotools arch=linux-rocky9-zen3 %c=gcc@14.2.0
 -   z5b4cvr      ^m4@1.4.20+sigsegv build_system=autotools arch=linux-rocky9-zen3 %c,cxx=gcc@14.2.0
 -   fmmjoru          ^libsigsegv@2.14 build_system=autotools arch=linux-rocky9-zen3 %c=gcc@14.2.0
 -   upkermr      ^netlib-lapack@3.12.1~external-blas~ipo+lapacke+pic+shared~xblas build_system=cmake build_type=Release generator=make patches:=3059ebf,b1af8b6,e318340 arch=linux-rocky9-zen3 %c,fortran=gcc@14.2.0
 -   hlyymsz          ^cmake@3.31.8~doc+ncurses+ownlibs~qtgui build_system=generic build_type=Release arch=linux-rocky9-zen3 %c,cxx=gcc@14.2.0
 -   fck5gif              ^curl@8.11.1~gssapi~ldap~libidn2~librtmp~libssh~libssh2+nghttp2 build_system=autotools libs:=shared,static tls:=openssl arch=linux-rocky9-zen3 %c,cxx=gcc@14.2.0
 -   szxxvba                  ^nghttp2@1.65.0 build_system=autotools arch=linux-rocky9-zen3 %c,cxx=gcc@14.2.0
 -   gcuml5o              ^ncurses@6.5~symlinks+termlib abi=none build_system=autotools patches:=7a351bc arch=linux-rocky9-zen3 %c,cxx=gcc@14.2.0
[e]  5lrqru4      ^openmpi@5.0.7+atomics~cuda~debug+fortran~gpfs~internal-hwloc~internal-libevent~internal-pmix~ipv6~java~lustre~memchecker~openshmem~rocm~romio+rsh~static~two_level_namespace+vt+wrapper-rpath build_system=autotools fabrics:=none patches:=38529b5 romio-filesystem:=none schedulers:=none arch=linux-rocky9-zen3 
 -   67uoyov      ^slepc@3.23.2+arpack~blopex~cuda~hpddm~rocm build_system=generic arch=linux-rocky9-zen3 %c,cxx,fortran=gcc@14.2.0
 -   zzdvpn7          ^arpack-ng@3.9.1~icb~ipo+mpi+shared build_system=cmake build_type=Release generator=make arch=linux-rocky9-zen3 %c,cxx,fortran=gcc@14.2.0
 -   ofyy24q              ^openblas@0.3.29~bignuma~consistent_fpcsr+dynamic_dispatch+fortran~ilp64+locking+pic+shared build_system=makefile symbol_suffix=none threads=none arch=linux-rocky9-zen3 %c,cxx,fortran=gcc@14.2.0
 -   sydctka          ^petsc@3.23.4~X~batch~cgns~complex~cuda~debug+double+examples~exodusii~fftw+fortran~giflib+hdf5~hpddm~hwloc+hypre~int64~jpeg~knl~kokkos~libpng~libyaml~memkind+metis~mkl-pardiso~mmg~moab~mpfr+mpi~mumps~openmp~p4est~parmmg~ptscotch~random123~rocm~saws~scalapack+shared~strumpack~suite-sparse+superlu-dist~sycl~tetgen~trilinos~valgrind~zoltan build_system=generic clanguage=C memalign=none arch=linux-rocky9-zen3 %c,cxx,fortran=gcc@14.2.0
 -   qzdqk62              ^hdf5@1.14.6~cxx~fortran~hl~ipo~java~map+mpi+shared~subfiling~szip~threadsafe+tools api=default build_system=cmake build_type=Release generator=make arch=linux-rocky9-zen3 %c=gcc@14.2.0
 -   ucsejlx              ^hypre@2.33.0~caliper~complex~cuda~debug+fortran~gptune~gpu-aware-mpi~gpu-profiling~int64~internal-superlu+lapack~magma~mixedint+mpi~openmp~rocm+shared~superlu-dist~sycl~umpire~unified-memory build_system=autotools precision=double arch=linux-rocky9-zen3 %c,fortran=gcc@14.2.0
 -   emygy6i              ^metis@5.1.0~gdb~int64~ipo~no_warning~real64+shared build_system=cmake build_type=Release generator=make patches:=4991da9,93a7903,b1225da arch=linux-rocky9-zen3 %c,cxx=gcc@14.2.0
 -   ueyq3hb              ^parmetis@4.0.3~gdb~int64~ipo+shared build_system=cmake build_type=Release generator=make patches:=4f89253,50ed208,704b84f arch=linux-rocky9-zen3 %c,cxx=gcc@14.2.0
 -   6uw66kg              ^superlu-dist@9.1.0~cuda~int64~ipo~openmp+parmetis~rocm+shared build_system=cmake build_type=Release generator=make arch=linux-rocky9-zen3 %c,cxx,fortran=gcc@14.2.0
 -   ljagv2z          ^python@3.13.5+bz2+ctypes+dbm~debug+libxml2+lzma~optimizations+pic+pyexpat+pythoncmd+readline+shared+sqlite3+ssl~tkinter+uuid+zlib build_system=generic arch=linux-rocky9-zen3 %c,cxx=gcc@14.2.0
 -   zajbf37              ^expat@2.7.1+libbsd build_system=autotools arch=linux-rocky9-zen3 %c,cxx=gcc@14.2.0
 -   kdfx7bj                  ^libbsd@0.12.2 build_system=autotools arch=linux-rocky9-zen3 %c=gcc@14.2.0
 -   xzhtca4                      ^libmd@1.1.0 build_system=autotools arch=linux-rocky9-zen3 %c=gcc@14.2.0
 -   2cde5gq              ^libffi@3.4.8 build_system=autotools arch=linux-rocky9-zen3 %c,cxx=gcc@14.2.0
 -   sirdtvz              ^openssl@3.4.1~docs+shared build_system=generic certs=mozilla arch=linux-rocky9-zen3 %c,cxx=gcc@14.2.0
 -   aswdeyh                  ^ca-certificates-mozilla@2025-05-20 build_system=generic arch=linux-rocky9-zen3 
 -   jtxfdbh              ^pkgconf@2.3.0 build_system=autotools arch=linux-rocky9-zen3 %c=gcc@14.2.0
 -   37jm4hs              ^readline@8.2 build_system=autotools patches:=1ea4349,24f587b,3d9885e,5911a5b,622ba38,6c8adf8,758e2ec,79572ee,a177edc,bbf97f1,c7b45ff,e0013d9,e065038 arch=linux-rocky9-zen3 %c=gcc@14.2.0
 -   jw6qrnk              ^sqlite@3.46.0+column_metadata+dynamic_extensions+fts~functions+rtree build_system=autotools arch=linux-rocky9-zen3 %c=gcc@14.2.0
 -   p3ueeka              ^util-linux-uuid@2.41 build_system=autotools arch=linux-rocky9-zen3 %c=gcc@14.2.0
 -   ov5pfld              ^xz@5.6.3~pic build_system=autotools libs:=shared,static arch=linux-rocky9-zen3 %c=gcc@14.2.0
```
Isto mostra tudo o que vai ser instalado, com as suas versões e opções. Parece quase um novo sistema operacional inteiro, e quase é, mas quando você for instalar outros pacotes, mesmo em outros ambientes, o Spack reaproveita dependências já instaladas e não as instala novamente. Por isto que pedi 12 horas para este shell, e 8 processadores. Parece exagerado, mas você nunca sabe.


Basta agora compilar o pacote. Vamos colocar os 8 processadores disponíveis para o sistema de compilação.


