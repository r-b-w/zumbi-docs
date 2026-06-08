---
date: '2026-03-24T18:43:16-03:00'
draft: false
title: 'Python'
weight: '30'
cascade:
    type: docs
---

Para o bem ou para o mal, e muito influenciado pela histeria coletiva causada pela IA, a linguagem [Python](http://www.python.org) tornou-se muito importante na programação científica. Há várias versões disponíveis no Zumbi, mas talvez a melhor opção seja não usar nenhuma delas.

# Sistema
No nó de login existe a versão nativa do sistema operacional, usada principalmente em utilitários e para gerência do sistema. Você não deve contar com nenhuma característica desta versão.

```console
[ramiro@zumbi ~]$ which python
/usr/bin/python
[ramiro@zumbi ~]$ /usr/bin/python --version
Python 3.9.25
[ramiro@zumbi ~]$
```
Nos nós computacionais, nem isto existe, propositalmente.
```console
[ramiro@zumbi ~]$ srun ls /usr/bin/python
/usr/bin/ls: cannot access '/usr/bin/python': No such file or directory
srun: error: n01: task 0: Exited with exit code 2
[ramiro@zumbi ~]$
```
É melhor realmente não usar isto para nada. Apesar do OpenHPC fornecer alguns módulos interessantes relacionados ao Python, eles são relativos ao Python do sistema, portanto travados em uma versão específica. É melhor você ignorar isto também. A boa prática atual para desenvolvimento de projetos em Python é usando ambientes virtuais, nos quais você instala uma versão específica do Python e todos os módulos necessários em suas versões específicas e compatíveis entre si, isoladas dos requisitos dos outros projetos. Há **incontáveis** alternativas para fazer isto.

# Família Conda

`conda` é um gerenciador de pacotes para o Python, isto é, você usa o `conda` para instalar um módulo, e ele automaticamente instala todas as dependências daquele pacote, em versões compatíveis, automaticamente. Para programas grandes e complexos, isto não é nada trivial. Os pacotes são armazenados em repositórios acessíveis via internet e, tipicamente, são descarregados apenas quando sua instalação é requisitada. O gerenciador conda é usado por dois sistemas, pelo menos, Anaconda e miniconda.


## Anaconda
O [Anaconda](https://www.anaconda.com/) foi uma das primeiras distribuições de Python visando programação científica que integra basicamente todos os módulos que uma pessoa normal pode desejar. É o que eu recomendo se você tem um computador potente em casa, e quer uma coisa fácil de usar, já que tem uma interface gráfica. Os maiores problemas do Anaconda são o tamanho do sistema, sua lentidão e controle dos repositórios por uma companhia.

## Miniconda
O [miniconda](https://www.anaconda.com/docs/getting-started/miniconda/main) é um sistema infinitamente menor do que o Anaconda, que usa os mesmos repositórios. Sua interface é apenas de linha de comando e quando instalado descarrega apenas uma versão do Python e um conjunto pequeno de módulos utilitários, em contraste com o Anaconda que instala Gigabytes. Na escolha entre estes dois, o miniconda é a minha recomendação.

## Mamba
O `conda` é uma maravilha da natureza, mas o seu algoritmo de resolução de dependências nem sempre foi o melhor possível. Eu pessoalmente já tive situações onde a remoção ou instalação de um módulo levou várias horas ou sequer terminou, e eu preferi remover o ambiente virtual completamente e recriá-lo do zero. As coisas podem ter melhorado recentemente. Devido a este problema, e também ao facilidade de criar e usar repositórios públicos, foi criado o [Mamba](https://mamba.readthedocs.io/en/latest/#), que é uma alternativa drop-in para o miniconda, escrito em C++ e devido isto e a outras escolhas de projeto é muito, muito mais rápido e usa muito menos memória que o conda original. [Aqui](mamba) há um exemplo de instalação e uso do mamba. É provavelmente o mais adequado para um usuário normal do Zumbi.

## Micromamba
Se você considera o Mamba grande e complicado de instalar (não é), existe ainda o [micromamba](https://mamba.readthedocs.io/en/latest/installation/micromamba-installation.html) que é um programa em C++ linkado estaticamente, que você só descarrega no seu computador e executa. Não tem nada mais simples. O micromamba nem precisa de um ambiente default, o que os outros todos precisam e às vezes é inconveniente por conflitar com outros sistemas que instalam sua própria versão do Python. O micromamba, é claro, é a minha escolha para o meu uso doméstico.

# UV
O [uv](https://docs.astral.sh/uv/) é um dos  mais modernos gerenciadores de pacotes e projetos para Python, que explodiu em popularidade recentemente. É escrito em Rust, não que isto seja alguma qualidade intrínseca. Não tem o mesmo foco em programação científica como os outros descritos anteriormente, mas é provavelmente a melhor escolha para projetos em Python de programação geral. É o que eu uso se, por exemplo, eu não espero que exista um pacote de HDF5 em Python pronto para instalação (o pior é que tem). A maior vantagem do `uv` é automatização da criação de projetos, executáveis, scripts ou bibliotecas, que os outros ou não tem, ou é deficiente. Se tudo o que você precisa instalar existe no PyPI, isto é, pode ser instalado com `pip`, o `uv` é uma escolha muito boa, porém é um programa bastante complexo e sofisticado, com uma curva de aprendizagem não trivial. Temos um também um  [exemplo de uso do uv](uv) 

