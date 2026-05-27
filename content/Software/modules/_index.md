---
date: '2026-03-24T18:43:16-03:00'
draft: true
title: 'Módulos OpenHPC'
weight: '10'
cascade:
    type: docs
---
Muito software para desenvolvimento de programas científicos, componentes do [OpenHPC](https://openhpc.community/), já está instalado no Zumbi. Muitos deste pacotes são conflitantes entre si, então o OpenHPC usa um [sistema de módulos](https://lmod.readthedocs.io/en/latest/index.html) para torná-los disponíveis sob demanda. A interface do usuário com o sistema de módulos é através do comando `module`.

Vamos examinar rapidamente alguns dos principais comandos, mas é muito recomendável que você pelo menos uma vez na vida olhe de passagem o [guia do usuário](https://lmod.readthedocs.io/en/latest/010_user.html). Você também sempre pode digitar `module help`, pelo menos para lembrar quais subcomandos existem. Uma das ideias centrais deste sistema é que você só pode ter um família de compiladores e uma implementação de MPI ativas em um determinado ambiente. Nada impede, no entanto, que você tenha vários shell abertos simultaneamente, cada um com um conjunto diferente de módulos instalado.

# Módulos instalados 
Os sistema é configurado com um conjunto razoável de módulos pré-instalados para todos os usuários, que permitem que você comece a desenvolver programas paralelos imediatamente. Use `module list` para ver quais módulos estão disponíveis para você naquele instante. Vou mostrar a minha configuração pessoal aqui, que pode ser diferente da configuração padrão. 

```console 
[ramiro@zumbi ~]$ module list

Currently Loaded Modules:
  1) autotools   3) gnu14/14.2.0   5) ucx/1.18.0         7) openmpi5/5.0.7
  2) prun/2.2    4) hwloc/2.12.0   6) libfabric/1.18.0   8) ohpc
```
Vemos que, se eu não mudar nada, meus compiladores serão da família Gnu GCC 14, e a biblioteca MPI padrão que será usada é OpenMPI. Isto é o que foi usando na demonstração da [execução de um programa MPI](/slurm/mpi).

# Módulos disponíveis
Você pode ver o que está disponível no sistema com `module avail`. No momento, isto produz
```console 
[ramiro@zumbi ~]$ module avail

---------------------------------------- /data/Programas/modulefiles -----------------------------------------
   gem-2017.10    gem-2025.10    imex-2017.10    imex-2025.10

---------------------------------- /opt/ohpc/pub/moduledeps/gnu14-openmpi5 -----------------------------------
   adios2/2.10.1    imb/2021.3                  omb/7.5                ptscotch/7.0.7      sionlib/1.7.7
   boost/1.88.0     mfem/4.4                    opencoarrays/2.10.2    py3-mpi4py/3.1.5    slepc/3.18.0
   dimemas/5.4.2    mumps/5.2.1                 otf2/3.1.1             py3-scipy/1.5.4     superlu_dist/6.4.0
   extrae/3.8.3     netcdf-cxx/4.3.1     (D)    petsc/3.18.1           scalapack/2.2.2     tau/2.31.1
   fftw/3.3.10      netcdf-fortran/4.6.2 (D)    phdf5/1.14.6           scalasca/2.6.2      trilinos/13.4.0
   hypre/2.33.0     netcdf/4.9.3         (D)    pnetcdf/1.14.0         scorep/9.0

--------------------------------------- /opt/ohpc/pub/moduledeps/gnu14 ---------------------------------------
   R/4.5.0         hdf5/1.14.6        mvapich2/2.3.7          openblas/0.3.29         scotch/7.0.7
   cubelib/4.9     impi/2021.11       netcdf-cxx/4.3.1        openmpi5/5.0.7   (L)    superlu/7.0.0
   cubew/4.9       likwid/5.4.1       netcdf-fortran/4.6.2    pdtoolkit/3.25.1
   gotcha/1.0.8    metis/5.1.0        netcdf/4.9.3            plasma/24.8.7
   gsl/2.8         mpich/3.4.3-ofi    opari2/2.0.9            py3-numpy/1.26.4

--------------------------------------- /opt/ohpc/pub/moduledeps/spack ---------------------------------------
   paraview/5.13.1-gcc-14.2.0-linux-rocky9-bulldozer-hp6xnum

----------------------------------------- /opt/ohpc/pub/modulefiles ------------------------------------------
   EasyBuild/5.1.2          gnu15/15.2.0                 intel/2025.1.1          papi/6.0.0
   autotools         (L)    hwloc/2.12.0          (L)    intel/2025.3.2          pmix/4.2.9
   charliecloud/0.40        intel/2023.2.1.rpmnew        intel/2025.3.3          prun/2.2        (L)
   cmake/4.1.2              intel/2023.2.1               intel/2026.0.0          ucx/1.18.0      (L)
   flex/2.6.4               intel/2024.0.0.rpmnew        libfabric/1.18.0 (L)    valgrind/3.25.1
   gnu12/12.2.0             intel/2024.0.0        (D)    ohpc             (L)
   gnu14/14.2.0      (L)    intel/2025.0.4               os

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
Use `module load` para carregar um módulo.

```console 
[ramiro@zumbi ~]$ module load trilinos
[ramiro@zumbi ~]$ module list

Currently Loaded Modules:
  1) autotools   3) gnu14/14.2.0   5) ucx/1.18.0         7) openmpi5/5.0.7   9) openblas/0.3.29
  2) prun/2.2    4) hwloc/2.12.0   6) libfabric/1.18.0   8) ohpc            10) trilinos/13.4.0
```
Pronto, agora você pode desenvolver programas científicos em C++ usando a biblioteca [Trilinos](https://trilinos.github.io/). Quem já tentou instalar uma biblioteca como esta a partir do código fonte, "na mão", vai entender que literalmente dias de trabalho foram economizados.

# Descarregando um módulo
Você pode querer descarregar um módulo, por não precisar mais dele, ou para instalar outro equivalente ou incompatível com ele. Use `module del`.

```console 
[ramiro@zumbi ~]$ module del trilinos
[ramiro@zumbi ~]$ module list

Currently Loaded Modules:
  1) autotools   3) gnu14/14.2.0   5) ucx/1.18.0         7) openmpi5/5.0.7
  2) prun/2.2    4) hwloc/2.12.0   6) libfabric/1.18.0   8) ohpc
```

# Compiladores Intel
Há quem prefira usar compiladores da Intel, mesmo estando aberto a discussão se eles estão gerando o código mais eficiente possível para processadores AMD como os que estão instalados no Zumbi. Pesquise como setar manualmente flags de compilação para a arquitetura EPYC, caso contrário os compiladores Intel geram código para o processador X86-64 mais simples possível, deixando muito desempenho.

De qualquer forma, para usar um compilador intel, você precisa "trocar" o módulo do GCC por uma módulo da Intel, com  `module swap`. É necessário também trocar o módulo MPI pelo da Intel.

```console 
[ramiro@zumbi ~]$ module swap openmpi5 impi
Loading modulefiles version 2021.11
[ramiro@zumbi ~]$ module swap gnu14 intel
Removing modulefiles version 2021.11
Use `module list` to view any remaining dependent modules.
Loading modulefiles version 2021.11
Removing modulefiles version 2021.11
Use `module list` to view any remaining dependent modules.
Loading modulefiles version 2021.11

Due to MODULEPATH changes, the following have been reloaded:
  1) impi/2021.11
```
Vamos ver quem é o compilador mpi agora,

```console 
[ramiro@zumbi ~]$ which mpicc
/opt/intel/oneapi/mpi/2021.11/bin/mpicc
[ramiro@zumbi ~]$ 
```

Podemos repetir o teste com o [programa exemplo MPI](/slurm/mpi).
```console 
[ramiro@zumbi tmp2]$ ls
bcast.c
[ramiro@zumbi tmp2]$ srun mpicc -o bcast bcast.c
[ramiro@zumbi tmp2]$ ldd ./bcast
	linux-vdso.so.1 (0x00007ffd01952000)
	libmpifort.so.12 => /opt/intel/oneapi/mpi/2021.11/lib/libmpifort.so.12 (0x00007f1651000000)
	libmpi.so.12 => /opt/intel/oneapi/mpi/2021.11/lib/libmpi.so.12 (0x00007f164f400000)
	libm.so.6 => /lib64/libm.so.6 (0x00007f165142e000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f164f000000)
	librt.so.1 => /lib64/librt.so.1 (0x00007f1651426000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007f165141e000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f165151e000)
```
Claramente estamos usando as biblotecas da Intel agora. Para executar o programa, temos que usar o `mpiexec` ou `mpirun` da Intel.

```console 
[ramiro@zumbi tmp2]$ salloc --ntasks=8
salloc: Granted job allocation 2662
salloc: Nodes n01 are ready for job
[ramiro@n01 tmp2]$ mpirun ./bcast
Root process (Rank 0) on host 'n01' is broadcasting value: 99
Process 0 on host 'n01' received broadcasted value: 99
Process 1 on host 'n01' received broadcasted value: 99
Process 2 on host 'n01' received broadcasted value: 99
Process 3 on host 'n01' received broadcasted value: 99
Process 4 on host 'n01' received broadcasted value: 99
Process 5 on host 'n01' received broadcasted value: 99
Process 6 on host 'n01' received broadcasted value: 99
Process 7 on host 'n01' received broadcasted value: 99
[ramiro@n01 tmp2]$ 
```
Não é super óbvio que o MPI da Intel vai usar o hardware de comunicação Infiniband automaticamente como o OpenMPI, aqui neste sistema. Informações são bem-vindas.

