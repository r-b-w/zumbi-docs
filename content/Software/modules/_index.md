---
date: '2026-03-24T18:43:16-03:00'
draft: true
title: 'Módulos OpenHPC'
weight: '10'
cascade:
    type: docs
---
Muito software para desenvolvimento de programas científicos, componentes do [OpenHPC](https://openhpc.community/), já está instalado no Zumbi. Muitos deste pacotes são conflitantes entre si, então o OpenHPC usa um [sistema de módulos](https://lmod.readthedocs.io/en/latest/index.html) para torná-los disponíveis sob demanda. A interface do usuário com o sistema de módulos é através do comando `module`. Você também pode olhar os [componentes do OpenHPC](https://github.com/openhpc/ohpc/wiki/Component-List-v3.x), e se houver algo que você deseje que seja instalado, fale conosco e vamos ver o que é possível fazer.

Vamos examinar rapidamente alguns dos principais comandos, mas é muito recomendável que você pelo menos uma vez na vida olhe de passagem o [guia do usuário](https://lmod.readthedocs.io/en/latest/010_user.html). Você também sempre pode digitar `module help`, pelo menos para lembrar quais subcomandos existem. Uma das ideias centrais deste sistema é que você só pode ter um família de compiladores e uma implementação de MPI ativas em um determinado ambiente. Nada impede, no entanto, que você tenha vários shell abertos simultaneamente, cada um com um conjunto diferente de módulos instalado.

# Módulos instalados 
Os sistema é configurado com um conjunto razoável de módulos pré-instalados para todos os usuários, que permitem que você comece a desenvolver programas paralelos imediatamente. Use `module list` para ver quais módulos estão disponíveis para você naquele instante. Vou mostrar a minha configuração pessoal aqui, que pode ser diferente da configuração padrão. 

```console 
[ramiro@n01 ~]$ module list

Currently Loaded Modules:
  1) autotools   3) gnu15/15.2.0   5) ucx/1.18.0         7) openmpi5/5.0.7
  2) prun/2.2    4) hwloc/2.12.0   6) libfabric/1.18.0   8) ohpc
```
Vemos que, se eu não mudar nada, meus compiladores serão da família Gnu GCC 15, e a biblioteca MPI padrão que será usada é OpenMPI. Isto é o que foi usando na demonstração da [execução de um programa MPI](/slurm/mpi).

# Módulos disponíveis
Você pode ver o que está disponível no sistema com `module avail`. No momento, isto produz
```console 
[ramiro@n01 ~]$ module avail

------------------------------------- /data/Programas/modulefiles --------------------------------------
   gem-2017.10    gem-2025.10    imex-2017.10    imex-2025.10

------------------------------- /opt/ohpc/pub/moduledeps/gnu15-openmpi5 --------------------------------
   adios2/2.10.2    mfem/4.4                    petsc/3.23.5        scorep/9.3
   boost/1.88.0     mumps/5.8.1                 phdf5/1.14.6        sionlib/1.7.7
   dimemas/5.4.2    netcdf-cxx/4.3.1            pnetcdf/1.14.0      slepc/3.23.0
   extrae/3.8.3     netcdf-fortran/4.6.2        ptscotch/7.0.7      superlu_dist/6.4.0
   fftw/3.3.10      netcdf/4.9.3         (D)    py3-mpi4py/3.1.5    tau/2.34.1
   hypre/2.33.0     omb/7.5                     scalapack/2.2.2     trilinos/16.1.0
   imb/2021.10      otf2/3.1.1                  scalasca/2.6.2

------------------------------------ /opt/ohpc/pub/moduledeps/gnu15 ------------------------------------
   R/4.5.0         gsl/2.8          impi/2021.17 (D)    opari2/2.0.9            plasma/24.8.7
   cubelib/4.9     hdf5/1.14.6      likwid/5.4.1        openblas/0.3.30         py3-numpy/1.26.4
   cubew/4.9       impi/2021.9.0    metis/5.1.0         openmpi5/5.0.7   (L)    scotch/7.0.7
   gotcha/1.0.8    impi/2021.14     netcdf/4.9.3        pdtoolkit/3.25.1        superlu/7.0.0

------------------------------------ /opt/ohpc/pub/moduledeps/spack ------------------------------------
   paraview/5.13.1-gcc-14.2.0-linux-rocky9-bulldozer-hp6xnum

-------------------------------------- /opt/ohpc/pub/modulefiles ---------------------------------------
   EasyBuild/5.1.2          gnu14/14.2.0          intel/2025.1.1          papi/6.0.0
   autotools         (L)    gnu15/15.2.0   (L)    intel/2025.3.2          pmix/4.2.9
   charliecloud/0.40        hwloc/2.12.0   (L)    intel/2025.3.3          prun/2.2        (L)
   cmake/4.1.2              intel/2023.2.1        libfabric/1.18.0 (L)    spack/1.0.2
   flex/2.6.4               intel/2024.0.0 (D)    ohpc             (L)    ucx/1.18.0      (L)
   gnu12/12.2.0             intel/2025.0.4        os                      valgrind/3.25.1

  Where:
   D:  Default Module
   L:  Module is loaded

If the avail list is too long consider trying:

"module --default avail" or "ml -d av" to just list the default modules.
"module overview" or "ml ov" to display the number of modules for each name.

Use "module spider" to find all possible modules and extensions.
Use "module keyword key1 key2 ..." to search for all possible modules matching any of the "keys".
```
Esta list pode mudar ao longo do tempo, quando o sistema é atualizado.

# Carregando um módulo
Use `module load` para carregar um módulo. Vamos supor que você queira usar o cmake e compilar programas C++ usando a biblioteca [Trilinos](https://trilinos.github.io/).

```console 
[ramiro@n01 ~]$ module load cmake trilinos
[ramiro@n01 ~]$ module list

Currently Loaded Modules:
  1) autotools      4) hwloc/2.12.0       7) openmpi5/5.0.7  10) openblas/0.3.30
  2) prun/2.2       5) ucx/1.18.0         8) ohpc            11) trilinos/16.1.0
  3) gnu15/15.2.0   6) libfabric/1.18.0   9) cmake/4.1.2
```
Pronto, agora você pode desenvolver programas científicos em C++ usando a biblioteca Trilinos. Quem já tentou instalar uma biblioteca como esta a partir do código fonte, "na mão", vai entender que literalmente dias de trabalho foram economizados.

# Descarregando um módulo
Você pode querer descarregar um módulo, por não precisar mais dele, ou para instalar outro equivalente ou incompatível com ele. Use `module del`.

```console 
ramiro@n01 ~]$ module del cmake
[ramiro@n01 ~]$ module list

Currently Loaded Modules:
  1) autotools   3) gnu15/15.2.0   5) ucx/1.18.0         7) openmpi5/5.0.7   9) openblas/0.3.30
  2) prun/2.2    4) hwloc/2.12.0   6) libfabric/1.18.0   8) ohpc            10) trilinos/16.1.0
```

# Compiladores Intel

Há quem prefira usar compiladores da Intel. Se este for seu caso, por favor atente para o fato de que a Intel, historicamente, não se preocupa em gerar, automaticamente, o código mais eficiente possível para processadores AMD. Para processadores que não são da Intel, os compiladores desta família tendem a gerar códigos para um arquitetura muito básica, que não usa os as instruções mais rápidas do processador. Se você deseja usar compiladores da Intel, por favor configure a arquitetura alvo na linha de comando com flags de compilação do tipo `-march=core-avx2 -fma -ftz`   (gerado pelo Gemini, verifique você mesmo!)

De qualquer forma, para usar um compilador intel, você precisa "trocar" o módulo do GCC por uma módulo da Intel, com  `module swap`. 

```console 
[ramiro@n01 ~]$ module swap gnu15 intel/2025.3

Inactive Modules:
  1) openblas

Due to MODULEPATH changes, the following have been reloaded:
  1) openmpi5/5.0.7     2) trilinos/16.1.0

[ramiro@n01 ~]$ module list

Currently Loaded Modules:
  1) autotools    5) compiler-rt/2025.3.3   9) intel/2025.3.3    13) openmpi5/5.0.7
  2) prun/2.2     6) umf/1.0.2             10) hwloc/2.12.0      14) trilinos/16.1.0
  3) ohpc         7) compiler/2025.3.3     11) ucx/1.18.0
  4) tbb/2023.0   8) mkl/2025.0            12) libfabric/1.18.0

Inactive Modules:
  1) openblas
```

Perceba que o sistema adaptou automaticamente alguns outros módulos para versões compatíveis com o compilador da Intel.

Vamos ver quem é o compilador mpi agora,

```console 
[ramiro@n01 tmp2]$ which mpicc
/opt/ohpc/pub/mpi/openmpi5-intel/5.0.7/bin/mpicc
[ramiro@n01 tmp2]$ mpicc --version
Intel(R) oneAPI DPC++/C++ Compiler 2025.3.3 (2025.3.3.20260319)
Target: x86_64-unknown-linux-gnu
Thread model: posix
InstalledDir: /opt/intel/oneapi/compiler/2025.3/bin/compiler
Configuration file: /opt/intel/oneapi/compiler/2025.3/bin/compiler/../icx.cfg
```

Podemos repetir o teste com o [programa exemplo MPI](/slurm/mpi).
```console 
[ramiro@n01 tmp2]$ mpicc -o bcast bcast.c
[ramiro@n01 tmp2]$ ls -l
total 140
-rwxr-xr-x 1 ramiro ramiro 137984 May 29 15:34 bcast
-rw-r--r-- 1 ramiro ramiro   1096 May 25 16:28 bcast.c
```
Vamos só verificar se está tudo sob controle. O shell que estou estou usando aqui tem 8 processadores alocados.

```console 
[ramiro@n01 tmp2]$ srun ./bcast
Process 2 on host 'n01' received broadcasted value: 99
Process 1 on host 'n01' received broadcasted value: 99
Process 6 on host 'n01' received broadcasted value: 99
Process 3 on host 'n01' received broadcasted value: 99
Process 5 on host 'n01' received broadcasted value: 99
Process 4 on host 'n01' received broadcasted value: 99
Process 7 on host 'n01' received broadcasted value: 99
Root process (Rank 0) on host 'n01' is broadcasting value: 99
Process 0 on host 'n01' received broadcasted value: 99
```
Aparentemente, tudo normal.

