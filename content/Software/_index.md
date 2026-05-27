---
date: '2026-03-24T18:43:16-03:00'
draft: true
title: 'Software'
weight: '40'
cascade:
    type: docs
---
Você deve considerar o software de sistema descrito aqui como imutável, no sentido de que é **extremamente improvável** que seja instalada alguma coisa que já não esteja instalada no cluster e que não seja de enorme interesse geral. Em princípio, você não deve também depender de versões específicas de pacotes ou bibliotecas do sistema operacional. A boa prática em sistemas de HPC é criar o seu próprio ambiente de desenvolvimento, preferencialmente com as ferramentas descritas nesta seção, o que permite que você migre seus projetos com mais facilidade quando o Zumbi não mais atender suas necessidade e você tiver que migrar para um sistema maior.

## Sistema Operacional
O sistema operacional usado no Zumbi, tanto no nó de login, quanto no nó master, é o [Rocky Linux](https://rockylinux.org/pt-BR) 9.X, com X >= 5. Este sistema promete compatibilidade binária com o Red Hat Linux 9.0, então existe uma grande probabilidade que programas précompilados que rodam no Red Hat rodem nele também, mas sempre é importante testar antes de fazer uma despesa contando com isto. Eu mesmo já tive problemas de compatibilidade no passado (não com o Rocky).

## Gerência do cluster
Apenas por curiosidade científica, o sistema de gerenciamento de cluster usado é o [Warewulf](https://warewulf.org/), versão 4.X, com X >=4. Este é o sistema que é responsável por criar as imagens que são usadas nos nós computacionais, carregar as imagens, gerir os usuários do nós computacionais e outras coisas. De forma alguma um usuário normal do cluster deve interagir diretamente com este sistema. Honestamente, um usuário normal não deveria nem saber que isto existe.

## Ambiente de HPC
Ao longo da história do desenvolvimento dos sistemas de HPC, foi-se percebendo uma comunalidade de interesses nos usuários de sistemas de HPC, em termos de compiladores, ambientes de desenvolvimento, bibliotecas para computação científica e utilitários. Muito esforço foi feito em muitas iniciativas independentes que foram mais ou menos bem sucedidas, na tentativa criar um sistema que facilitasse a instalação destes requisitos, de forma integrada. 

Um dos mais recentes e quem tido um razoável sucesso é o [OpenHPC](https://openhpc.community/). O Zumbi é mantido sobre o OpenHPC, na versão 3, inclusive a instalação do Warewulf que usamos é integrada ao OpenHPC.

Essencialmente, o OpenHPC é um conjunto de pacotes ".rpm" que são instalados e configurados para suporte ao desenvolvimento e uso de programas científicos de alto desempenho. 
Se você estiver curioso, pode dar uma olhada na [lista de componentes](https://github.com/openhpc/ohpc/wiki/Component-List-v3.x) desta versão do OpenHPC. Basicamente todos os componentes estão instalados no Zumbi.

## Sistema de módulos
Uma das principais características do OpenHPC é o sistema de módulos que permite que você use, por exemplo, diferentes implementações de MPI com difentes compiladores, com diferentes bibliotecas científicas. A interface do usuário com o sistema é através do comando `module`, que merece uma [seção separada](./modules).

## Ferramentas
Outras ferramentas úteis estão disponíveis no sistema, e outras, propositalmente, não.
Um outro enorme esforço para a padronização e a facilitação da instalação de software para programação científica e o [Spack](spack), e, para o bem ou para o mal, a linguagem [Python](python) tem tido um papel muito importante na programação científica também.

