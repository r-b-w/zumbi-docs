---
date: '2026-03-24T18:43:16-03:00'
draft: false
title: 'Mamba'
weight: 10
cascade:
    type: docs
---


É sempre melhor seguir as [instruções originais](https://mamba.readthedocs.io/en/latest/installation/mamba-installation.html) de instalação, mas vamos fazer um resumo aqui. Por motivos de compatibilidade, você pode usar `mamba` e `conda` como sinônimos.

Descarregue a versão mais recente do [miniforge](https://github.com/conda-forge/miniforge),
```console
[ramiro@zumbi ~]$ curl -L -O "https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-$(uname)-$(uname -m).sh"
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  100M  100  100M    0     0  35.9M      0  0:00:02  0:00:02 --:--:-- 66.7M
[ramiro@zumbi ~]$ ls -lh Miniforge3-Linux-x86_64.sh 
-rw-r--r-- 1 ramiro ramiro 101M jun  2 19:07 Miniforge3-Linux-x86_64.sh
```

Instale em um subdiretório de "~/data",
```console
[ramiro@zumbi ~]$ sh - Miniforge3-Linux-x86_64.sh -b -p ~/data/miniforge3 -c
#
# millions of lines deleted...
#
==> For changes to take effect, close and re-open your current shell. <==

Running `shell init`, which:
 - modifies RC file: "/home/ramiro/.bashrc"
 - generates config for root prefix: "/data/ramiro/miniforge3"
 - sets mamba executable to: "/data/ramiro/miniforge3/bin/mamba"
The following has been added in your "/home/ramiro/.bashrc" file

# >>> mamba initialize >>>
# !! Contents within this block are managed by 'mamba shell init' !!
export MAMBA_EXE='/data/ramiro/miniforge3/bin/mamba';
export MAMBA_ROOT_PREFIX='/data/ramiro/miniforge3';
__mamba_setup="$("$MAMBA_EXE" shell hook --shell bash --root-prefix "$MAMBA_ROOT_PREFIX" 2> /dev/null)"
if [ $? -eq 0 ]; then
    eval "$__mamba_setup"
else
    alias mamba="$MAMBA_EXE"  # Fallback on help from mamba activate
fi
unset __mamba_setup
# <<< mamba initialize <<<

[ramiro@zumbi ~]$
```

Você precisa agora sair e entrar novamente no Zumbi. Fazendo isto, e logando novamente, o prompt do sistema é
```console
(base) [ramiro@zumbi ~]$
```
o que indica que você está no ambiente base do Mamba. Você não quer **nunca** instalar nada aqui, isto inclusive pode quebrar o seu sistema. A melhor coisa é desativar a entrada automática no ambiente base, com o comando
```console
(base) [ramiro@zumbi ~]$ conda config --set auto_activate_base false
WARNING conda.cli.condarc:set_key(484): Key auto_activate_base is an alias of auto_activate; setting value with latter
(base) [ramiro@zumbi ~]$ 
```
Você precisa, mais uma vez, sair e entrar no Zumbi. Feito isto, o ambiente base não foi mais selecionado automaticamente, mas o conda está acessível
```console
[ramiro@zumbi ~]$ 
[ramiro@zumbi ~]$ mamba --help
Version: 2.5.0 



/data/ramiro/miniforge3/bin/mamba [OPTIONS] [SUBCOMMAND]


OPTIONS:
  -h,     --help              Print this help message and exit 
          --version           

Configuration options:
          --rc-file FILE1 FILE2... 
                              Paths to the configuration files to use 
          --no-rc             Disable the use of configuration files 
          --no-env            Disable the use of environment variables 

Global options:
  -v,     --verbose           Set verbosity (higher verbosity with multiple -v, e.g. -vvv) 
          --log-level ENUM:value in {critical->5,debug->1,error->4,info->2,off->6,trace->0,warning->3} OR {5,1,4,2,6,0,3} 
                              Set the log level 
  -q,     --quiet             Set quiet mode (print less output) 
  -y,     --yes               Automatically answer yes on prompted questions 
          --json              Report all output as json 
          --offline           Force use cached repodata 
          --dry-run           Only display what would have been done 
          --download-only     Only download and extract packages, do not link them into 
                              environment. 
          --experimental      Enable experimental features 
          --use-uv            Whether to use uv for installing pip dependencies. Defaults to 
                              false. 

Prefix options:
  -r,     --root-prefix PATH  Path to the root prefix 
  -p,     --prefix PATH       Path to the target prefix 
          --relocate-prefix PATH 
                              Path to the relocation prefix 
  -n,     --name NAME         Name of the target prefix 

SUBCOMMANDS:
  shell                       Generate shell init scripts 
  create                      Create new environment 
  install                     Install packages in active environment 
  update                      Update packages in active environment 
  repoquery                   Find and analyze packages in active environment or channels 
  remove, uninstall           Remove packages from active environment 
  list                        List packages in active environment 
  package                     Extract a package or bundle files into an archive 
  clean                       Clean package cache 
  config                      Configuration of micromamba 
  info                        Information about micromamba 
  constructor                 Commands to support using micromamba in constructor 
  env                         See `mamba/micromamba env --help` 
  activate                    Activate an environment 
  run                         Run an executable in an environment 
  ps                          Show, inspect or kill running processes 
  auth                        Login or logout of a given host 
  search                      Find packages in active environment or channels 
                              This is equivalent to `repoquery search` command 
[ramiro@zumbi ~]$
```

Vamos criar um ambiente, com a versão 3.14 do Python, instalando também o módulo [SymPy](https://www.sympy.org/en/index.html) para computação simbólica.

```console
[ramiro@zumbi ~]$ mamba env create -n numerics python=3.14 sympy
#
# many many many lines deleted...
# 
Transaction finished


To activate this environment, use:

    mamba activate numerics

Or to execute a single command in this environment, use:

    mamba run -n numerics mycommand
```

Seguindo o conselho acima, vamos ativar o ambiente e fazer um test rápido.
```console
[ramiro@zumbi ~]$ mamba activate numerics
(numerics) [ramiro@zumbi ~]$ python --version
Python 3.14.5
(numerics) [ramiro@zumbi ~]$ python
Python 3.14.5 | packaged by conda-forge | (main, May 20 2026, 00:15:36) [GCC 14.3.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import sympy
>>> from sympy.abc import a, t
>>> sympy.diff(sympy.cos(a*t), t)
-a*sin(a*t)
>>> 
```
Você pode sair do ambiente a qualquer momento,
```console
(numerics) [ramiro@zumbi ~]$ mamba deactivate
[ramiro@zumbi ~]$ 
```

Se você quiser instalar outros pacotes neste mesmo ambiente, basta lembrar-se de ativar o ambiente  e instalar o que você quiser.

```console
[ramiro@zumbi ~]$ mamba activate numerics
(numerics) [ramiro@zumbi ~]$ mamba install numpy
conda-forge/linux-64                                        Using cache
conda-forge/noarch                                          Using cache

Pinned packages:

  - python=3.14


Transaction

  Prefix: /data/ramiro/miniforge3/envs/numerics

  Updating specs:

   - numpy


  Package         Version  Build                Channel         Size
──────────────────────────────────────────────────────────────────────
  Install:
──────────────────────────────────────────────────────────────────────

  + libblas        3.11.0  8_h4a7cf45_openblas  conda-forge     19kB
  + libcblas       3.11.0  8_h0358290_openblas  conda-forge     19kB
  + libgfortran    15.2.0  h69a702a_19          conda-forge     28kB
  + libgfortran5   15.2.0  h68bc16d_19          conda-forge      2MB
  + liblapack      3.11.0  8_h47877c9_openblas  conda-forge     19kB
  + libopenblas    0.3.33  pthreads_h94d23a6_0  conda-forge      6MB
  + numpy           2.4.6  py314h2b28147_0      conda-forge      9MB

  Summary:

  Install: 7 packages

  Total download: 17MB

──────────────────────────────────────────────────────────────────────


Confirm changes: [Y/n] y

Transaction starting
libgfortran                                         27.7kB @  ??.?MB/s  0.1s
libblas                                             18.8kB @  ??.?MB/s  0.1s
liblapack                                           18.8kB @  ??.?MB/s  0.1s
libcblas                                            18.8kB @  ??.?MB/s  0.1s
libopenblas                                          5.9MB @   6.6MB/s  0.4s
libgfortran5                                         2.5MB @   3.1MB/s  0.4s
numpy                                                8.9MB @  10.1MB/s  0.5s
Linking libgfortran5-15.2.0-h68bc16d_19
Linking libgfortran-15.2.0-h69a702a_19
Linking libopenblas-0.3.33-pthreads_h94d23a6_0
Linking libblas-3.11.0-8_h4a7cf45_openblas
Linking libcblas-3.11.0-8_h0358290_openblas
Linking liblapack-3.11.0-8_h47877c9_openblas
Linking numpy-2.4.6-py314h2b28147_0

Transaction finished

(numerics) [ramiro@zumbi ~]$ 
```

Nem vou testar, não tem o que dar errado.

Você deve criar um ambiente para cada projeto ou conjunto de projetos intimamente relacionados. O Mamba tem muitas outras facilidades e você realmente deveria ler a [documentação](https://mamba.readthedocs.io/en/latest/user_guide/concepts.html).
