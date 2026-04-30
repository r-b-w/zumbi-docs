---
date: '2026-03-24T18:43:16-03:00'
draft: false
title: 'Slurm'
weight: 40
cascade:
    type: docs
---

O princípio fundamental de operação de um cluster computacional é que você não interage diretamente com os nós computacionais, para evitar a sobre-alocação de recursos. Recursos devem ser entendidos como tudo o que o seu programa usa quando roda, como processadores, memória, GPUs e tempo de execução. Clusters usam um *gerenciador de recursos*, no caso do Zumbi, o [Slurm](https://slurm.schedmd.com/), como mediador desta interação. 

O gerenciador de recursos é responsável por alocar, entre outras coisas, o número de processadores que o seu programa paralelo precisa. Caso os recursos requisitados não estejam disponíveis no momento da submissão do job, o gerenciador de recursos coloca seu job em uma fila de espera.
Os jobs são enfileirados por ordem de chegada, são executados assim que os recursos solicitados estiverem disponíveis. Em princípio, os jobs vão sendo executados de acordo com a ordem de submissão, mas o gerenciador pode ser configurado para "furar a fila" com um job que requisite menos recursos dos que aqueles que estão à sua frente. Por isto é recomendável fazer a requisição de recursos mais "apertada" possível, que ainda atenda às suas necessidades.

O Slurm é um sistema bastante sofisticado, com uma enorme variedade de funcionalidades. Vamos ver aqui o estritamente mínimo necessário para começar a usar o cluster, mas o estudo mais aprofundado da sua documentação é muito recomendável. Você pode começar pelo [guia rápido](https://slurm.schedmd.com/quickstart.html), e depois você pode imprimir esta [folha de resumo](https://slurm.schedmd.com/pdfs/summary.pdf) e deixar debaixo do seu travesseiro.

## Terminologia
A terminologia empregada no estudo da arquitetura de computadores é um pouco flexível, então vamos estabelecer um padrão válido para este documento. Esta terminologia é, a grosso modo, a mesma empregada pelo Slurm.
    
* Um **nó** é um computador completo independente, com uma placa mãe, um ou mais soquetes onde são instaladas CPUs, zero ou mais GPUs, memória e possivelmente discos. É a maior unidade de alocação de recursos, e, aqui no Zumbi, não deve ser requisitado ou alocado de maneira exclusiva.
* Cada nó tem um ou mais **soquetes**, onde são instaladas as CPUs. As CPUs são os processadores que você compra na loja, em uma caixinha, e instala na placa mãe. No Zumbi cada nó tem 2 soquetes e portanto duas CPUs. Não vamos usar soquetes ou CPUs como unidade de alocação.

* **Processadores**, que nesta documentação são entendidos como os *núcleos de processamento* que compõe as CPUs. Por exemplo, uma CPU AMD Athlon 3000G tem 2 núcleos (que custa uns 200 reais), uma CPU AMD EPYC 9965 tem 192 núcleos (e custa uns 14 mil dólares). É importante entender que em muitos contextos se usa a palavra "núcleo" ou "core", em inglês, para este dispositivo, mas para nós é mais convenientes chamá-los de "processadores", e estas são as "menores" unidades de alocação de processamento. Quando falarmos que estamos alocando "quatro processadores", na verdade estaremos alocando quatro núcleos computacionais, que podem estar no mesma CPU, em CPUs diferentes no mesmo nó, ou em CPUs diferentes de nós diferentes.

Em computação de alto desempenho não se usa hyperthreading, pois na enorme maioria das vezes seu uso prejudica o desempenho e complica o cálculo da eficiência. Em geral, nos clusters para HPC, e no Zumbi em particular, o hyperthreading é desativado no BIOS dos servidores. Um thread sempre deve ser executado por um processador (núcleo).

## Modelos de computação paralela

Para podermos alocar de maneira eficiente recursos de hardware a um programa paralelo, precisamos, acima de qualquer coisa, entender qual modelo de acesso à memória este programa usa. Um programa paralelo será sempre composto de um conjunto de processos que executam simultaneamente. Estes processos podem ou não trocar informações entre si, sendo que o problema de computação paralela infinitamente mais complexo quando existe a necessidade da troca de informações entre os processos. Este é, infelizmente, o caso mais típico quando tratamos de simulação computacional.

Para facilitar a minha vida, vamos ignorar completamente a existência de GPUs. Neste caso, existem dois grandes modelos de computação paralela, **memória compartilhada** e **memória distribuída**. Entendam que não estamos falando aqui de hardware, mas sim de *programas*, que serão, posteriormente, executados em hardware que implementa um destes modelos de computação paralela.

Nos modelos de computação que vamos discutir aqui, os processos são executadas de forma completamente independente, no sentido de que não há nenhuma sincronização além daquela imposta pelo programador. Os  processos iniciam, em princípio, em instantes diferentes, de acordo com o agendamento feito pelo sistema operacional. O programa paralelo começa quando o primeiro processo começa e termina quando o último processo termina.

### Memória compartilhada

No modelo de memória compartilhada, existe apenas um único espaço global de memória, e todas os processos tem acesso direto, para leitura e escrita, a qualquer endereço no espaço global de memória, como esquematizado abaixo. Nesta figura os vários processos estão sendo representados por

{{< figure
src="/images/shared_mem.svg"
alt="Modelo de memória compartilhada"
caption="Memória Compartilhada"
width="75%"
>}}

A troca de informações entre processos é feita através da escrita e leitura na memória global. É claro que é necessária a existência de mecanismos que proíbam ou pelo menos atribuam algum sentido à escrita por mais de um processo ao mesmo endereço de memória.

