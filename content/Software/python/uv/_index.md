---
date: '2026-03-24T18:43:16-03:00'
draft: false
title: 'uv'
weight: 20
cascade:
    type: docs
---

O [uv](https://docs.astral.sh/uv/) é um programa *bastante completo e complexo*. Deixem-me citar o primeiro item da lista de destaques da sua documentação:

- A single tool to replace pip, pip-tools, pipx, poetry, pyenv, twine, virtualenv, and more.

Uma única ferramenta que substitui sete outras, e mais, não pode ser simples. Você realmente precisa olhar com um pouco mais de cuidado a [documentação](https://docs.astral.sh/uv/).

## instalação
É sempre melhor seguir as [instruções originais](https://docs.astral.sh/uv/getting-started/installation/) de instalação, mas vamos fazer um resumo aqui. Basta fazer

```console
[ramiro@zumbi ~]$ curl -LsSf https://astral.sh/uv/install.sh | sh
downloading uv 0.11.18 x86_64-unknown-linux-gnu
installing to /home/ramiro/.local/bin
  uv
  uvx
everything's installed!
[ramiro@zumbi ~]$
```

Perceba que os programas foram instalados no diretório "${HOME}/.local/bin". Você precisa verificar se este diretório está na lista que é percorrida quando o shell procura um executável.
Simplesmente tente executar o programa `uv`,

```console
[ramiro@zumbi ~]$ uv
An extremely fast Python package manager.

Usage: uv [OPTIONS] <COMMAND>

Commands:
  auth       Manage authentication
  run        Run a command or script
  init       Create a new project
  add        Add dependencies to the project
  remove     Remove dependencies from the project
  version    Read or update the project's version
  sync       Update the project's environment
  lock       Update the project's lockfile
  export     Export the project's lockfile to an alternate format
  tree       Display the project's dependency tree
  format     Format Python code in the project
  check      Run checks on the project
  audit      Audit the project's dependencies
  tool       Run and install commands provided by Python packages
  python     Manage Python versions and installations
  pip        Manage Python packages with a pip-compatible interface
  venv       Create a virtual environment
  build      Build Python packages into source distributions and wheels
  publish    Upload distributions to an index
  workspace  Inspect uv workspaces
  cache      Manage uv's cache
  self       Manage the uv executable
  help       Display documentation for a command

Cache options:
  -n, --no-cache               Avoid reading from or writing to the cache, instead using a temporary directory for
                               the duration of the operation [env: UV_NO_CACHE=]
      --cache-dir <CACHE_DIR>  Path to the cache directory [env: UV_CACHE_DIR=]

Python options:
      --managed-python       Require use of uv-managed Python versions [env: UV_MANAGED_PYTHON=]
      --no-managed-python    Disable use of uv-managed Python versions [env: UV_NO_MANAGED_PYTHON=]
      --no-python-downloads  Disable automatic downloads of Python. [env: "UV_PYTHON_DOWNLOADS=never"]

Global options:
  -q, --quiet...
          Use quiet output
  -v, --verbose...
          Use verbose output
      --color <COLOR_CHOICE>
          Control the use of color in output [possible values: auto, always, never]
      --system-certs
          Whether to load TLS certificates from the platform's native certificate store [env: UV_SYSTEM_CERTS=]
      --offline
          Disable network access [env: UV_OFFLINE=]
      --allow-insecure-host <ALLOW_INSECURE_HOST>
          Allow insecure connections to a host [env: UV_INSECURE_HOST=]
      --no-progress
          Hide all progress outputs [env: UV_NO_PROGRESS=]
      --directory <DIRECTORY>
          Change to the given directory prior to running the command [env: UV_WORKING_DIR=]
      --project <PROJECT>
          Discover a project in the given directory [env: UV_PROJECT=]
      --config-file <CONFIG_FILE>
          The path to a `uv.toml` file to use for configuration [env: UV_CONFIG_FILE=]
      --no-config
          Avoid discovering configuration files (`pyproject.toml`, `uv.toml`) [env: UV_NO_CONFIG=]
  -h, --help
          Display the concise help for this command
  -V, --version
          Display the uv version

Use `uv help` for more details.
[ramiro@zumbi ~]$ 
```
O meu shell claramente já está configurado desta forma. Caso a resposta seja como abaixo,

```console
[ramiro@zumbi ~]$ uv
bash: uv: Arquivo ou diretório inexistente
[ramiro@zumbi ~]$
```
Você precisa editar o seu arquivo ".bashrc", adicionando ao final dele a linha a seguir,

```bash
export PATH="${PATH}:${HOME}/.local/bin"
```
Depois de modificar o arquivo ".bashrc" você precisa sair e voltar ao Zumbi.

## Instalação do Python
Por default, o `uv` usa o Python padrão do sistema, o que estamos querendo evitar. Então vamos instalar uma versão mais recente do Python, que o `uv` passará a usar automaticamente e ficará disponível para uso em qualquer ambiente. Também é possível instalar uma versão específica para uso geral e versões específicas diferentes em diferentes ambientes virtuais.

```console
[ramiro@zumbi ~]$ uv python install
Installed Python 3.12.11 in 223ms
 + cpython-3.12.11-linux-x86_64-gnu (python3.12)
[ramiro@zumbi ~]$ which python3.12
~/.local/bin/python3.12
```
Note que o Python 3.12 foi instalado no seu diretório local. É claro que quando você fizer isto a versão instalada pode ser diferente, ajuste o último comando acima adequadamente.

## Projetos
O conceito fundamental do uv é um [projeto](https://docs.astral.sh/uv/guides/projects/#viewing-your-version), que pode ser um script, um conjunto de scripts, um módulo ou um conjunto de módulos, com o registro de todas as versões de todas as dependências que o projeto necessita. Desta forma, o seu projeto pode ser reconstruído automaticamente em outro sistema. O `uv` ou outro programa equivalente descarrega, instala e compila, se for o caso, todas as dependências do seu projeto automaticamente. O `uv` também pode ser usado para criar uma distribuição binária ou do código fonte do seu projeto que outro usuário pode instalar e executar no seu sistema.

Vamos exemplificar com um projeto muito simples, com apenas um script. Primeiro criamos o diretório que vai armazenar o projeto.

```console
[ramiro@zumbi ~]$ mkdir UV
[ramiro@zumbi ~]$ cd UV
[ramiro@zumbi UV]$ uv init delete_me
Initialized project `delete-me` at `/home/ramiro/UV/delete_me`
```
Perceba que o `uv` tem suas próprias ideias sobre como os projetos dever ser chamados.
Vamos ver o que o `uv` fez

```console
[ramiro@zumbi delete_me]$ ls -la
total 28
drwxr-xr-x 3 ramiro ramiro 4096 jun  3 09:05 .
drwxr-xr-x 3 ramiro ramiro 4096 jun  3 09:05 ..
drwxr-xr-x 7 ramiro ramiro 4096 jun  3 09:05 .git
-rw-r--r-- 1 ramiro ramiro  109 jun  3 09:05 .gitignore
-rw-r--r-- 1 ramiro ramiro   87 jun  3 09:05 main.py
-rw-r--r-- 1 ramiro ramiro  155 jun  3 09:05 pyproject.toml
-rw-r--r-- 1 ramiro ramiro    5 jun  3 09:05 .python-version
-rw-r--r-- 1 ramiro ramiro    0 jun  3 09:05 README.md
[ramiro@zumbi delete_me]$
```
Note que o projeto já foi criado sob controle de versão do [git](https://git-scm.com/about), o que é muito bom. O uso do git está fora do escopo desta simplória introdução, no entanto. Você pode simplesmente ignorar isto por enquanto, se não souber do que estamos falando aqui, mas é um investimento importante se você pretende investir seu tempo em programação científica.

Neste projeto eu quero usar o NumPy e o SciPy, por exemplo, então eu preciso registrar estas dependências e instalar estes módulos no ambiente virtual criado para este projeto. Não é necessário ativar explicitamente o ambiente virtual, como fizemos com o Mamba.

```console
[ramiro@zumbi delete_me]$ uv add numpy scipy
Using CPython 3.12.11
Creating virtual environment at: .venv
Resolved 3 packages in 459ms
Prepared 2 packages in 2.12s
Installed 2 packages in 54ms
 + numpy==2.4.6
 + scipy==1.17.1
[ramiro@zumbi delete_me]$ 
```

O `uv` criou um ambiente virtual no subdiretório ".venv", e instalou o dois pacotes, que estão disponíveis para uso neste projeto. As dependências ficam registradas no arquivo 'pyproject.toml'
```toml
[project]
name = "delete-me"
version = "0.1.0"
description = "Add your description here"
readme = "README.md"
requires-python = ">=3.12"
dependencies = [
    "numpy>=2.4.6",
    "scipy>=1.17.1",
]
```
no qual você  deve editar adequadamente a descrição, e deve também escrever alguma coisa útil no arquivo "README.md".

O `uv` criou automaticamente o arquivo "main.py", a partir do qual você deve chamar as suas próprias funções. Recém criado, o arquivo é,

```python
def main():
    print("Hello from delete-me!")


if __name__ == "__main__":
    main()
```

Vamos fazer alguma coisa completamente inútil,
```python
import numpy as np 
from scipy.linalg import eig

def main():
    k = np.array([[60.0, -20.0, 0.0],[-20.0, 40.0, -20.0],[0.0, -20.0, 60.0]])
    m = np.array([[1.0, 0.0, 0.0],[0.0, 2.0, 0.0],[0.0, 0.0, 1.0]])
    w2, X = eig(k, m)
    w = np.sqrt(w2)
    print(f"Natural frequencies are {w}")
    print("Normal modes are")
    print(X)

if __name__ == "__main__":
    main()
```

Podemos executar este script com `uv run`,
```console
[ramiro@zumbi delete_me]$ uv run main.py
Natural frequencies are [3.42282467+0.j 7.74596669+0.j 8.26342975+0.j]
Normal modes are
[[ 3.57406744e-01  7.07106781e-01 -6.78598345e-01]
 [ 8.62856209e-01  1.36733616e-16  2.81084638e-01]
 [ 3.57406744e-01 -7.07106781e-01 -6.78598345e-01]]
[ramiro@zumbi delete_me]$ 
```
Lembre-se de usar o git para fazer o controle de versão neste diretório.
Se você pretende criar um projeto para uma biblioteca, deve usar `uv init --lib`.

Há muito mais coisa envolvida com o `uv`, por favor examine a extensa [documentação](https://docs.astral.sh/uv/guides/).
