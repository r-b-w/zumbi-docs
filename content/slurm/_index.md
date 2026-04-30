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

## Modelos de programação paralela

Para podermos alocar de maneira eficiente recursos de hardware a um programa paralelo, precisamos, acima de qualquer coisa, entender qual modelo de acesso à memória este programa usa. Um programa paralelo será sempre composto de um conjunto de processos que executam simultaneamente. Estes processos podem ou não trocar informações entre si, sendo que o problema de computação paralela infinitamente mais complexo quando existe a necessidade da troca de informações entre os processos. Este é, infelizmente, o caso mais típico quando tratamos de simulação computacional.

Para facilitar a minha vida, vamos ignorar completamente a existência de GPUs. Neste caso, existem dois grandes modelos de computação paralela, **memória compartilhada** e **memória distribuída**. Entendam que não estamos falando aqui de hardware, mas sim de *programas*, que serão, posteriormente, executados em hardware que implementa um destes modelos de computação paralela.

Nos modelos de computação que vamos discutir aqui, os processos são executadas de forma completamente independente, no sentido de que não há nenhuma sincronização além daquela imposta pelo programador. Os  processos iniciam, em princípio, em instantes diferentes, de acordo com o agendamento feito pelo sistema operacional. O programa paralelo começa quando o primeiro processo começa e termina quando o último processo termina.

### Memória compartilhada

No modelo de memória compartilhada, existe apenas um único espaço global de memória, e todas os processos tem acesso direto, para leitura e escrita, a qualquer endereço no espaço global de memória, como esquematizado abaixo. Nesta figura os vários processos estão sendo representados por \( T_i, i=1,\ldots,n\).

{{< figure
src="/images/shared_mem.svg"
alt="Modelo de memória compartilhada"
caption="Memória compartilhada"
width="75%"
>}}

A troca de informações entre processos é feita através da escrita e leitura na memória global. É claro que é necessária a existência de mecanismos que proíbam ou pelo menos atribuam algum sentido à escrita por mais de um processo ao mesmo endereço de memória.

Um programa de memória compartilhada pode ser escrito usando [Posix Threads](https://embarcados.com.br/threads-posix/) em uma linguagem sequencial como C ou Fortran, ou algum padrão como [OpenMP](https://www.openmp.org/), ou usando módulos específicos para uma determinada linguagem, como [Python Threads](https://docs.python.org/3/library/threading.html) e [Intel TBB](https://www-intel-com.translate.goog/content/www/us/en/developer/tools/oneapi/onetbb.html?_x_tr_sl=en&_x_tr_tl=pt&_x_tr_hl=pt&_x_tr_pto=tc).

### Memória distribuída

No modelo de memória distribuída, cada processo acessa apenas um espaço de memória local, completamente isolado e separado dos outros processos. A troca de informações entre processos, necessária para execução de programas paralelos, deve ser feita através da troca de mensagens, geralmente de forma explícita e geralmente controladas diretamente pelo programador. Este modelo está representado esquematicamente abaixo.

{{< figure
src="/images/dist_mem.svg"
alt="Modelo de memória distribuída"
caption="Memória distribuída"
width="75%"
>}}

Um programa de memória distribuída usualmente é escrito usando uma biblioteca para troca de mensagens como o [MPI](https://www.mpi-forum.org/) ou [Python Ray](https://docs.ray.io/en/latest/ray-core/walkthrough.html).

### Modelo híbrido

É possível desenvolver um programa paralelo usando um modelo híbrido, combinando os modelos de memória compartilhada e distribuída, onde os processos são organizados em grupos, nos quais os processos de cada grupo acessam diretamente um espaço de memória compartilhado, os grupos só podem trocar informações através da troca de mensagens.

{{< figure
src="/images/hibr_mem.svg"
alt="Modelo de memória híbrida"
caption="Memória híbrida"
width="75%"
>}}

Apesar de não ser exatamente uma lei da natureza, há um certo consenso de que a programação de sistemas com memória distribuída é mais complexa de que a programação de sistemas de memória compartilhada.

## Computadores paralelos

Nos termos mais gerais possíveis, um computador paralelo é um computador com mais de um processador.
Computadores paralelos podem ser construídos seguindo modelos análogos aos modelos de programação paralela: memória compartilhada, distribuída e híbridos.

A enorme maioria dos computadores modernos, considerando telefones celulares, computadores pessoais e laptops, são computadores de memória compartilhada, já que praticamente todos as  CPUs  em uso nestas aplicações tem mais de um núcleo e todos os núcleos acessam o mesmo espaço global de memória.

Praticamente todos os clusters de computadores modernos tem arquitetura híbrida, pois são compostos de servidores computacionais, com uma ou mais mais CPUs, onde cada CPU tem vários núcleos computacionais e todos os núcleos podem acessar toda a memória de cada servidor diretamente. Os servidores são conectados entre si por uma rede de alta velocidade, com tecnologia, Infiniband ou Ethernet, e a troca de informações entre servidores diferentes, durante a execução de um programa paralelo, é feita através de mensagens transmitidas por esta rede de comunicação.

A mais importante diferença prática entre os computadores de memória distribuída e compartilhada é a velocidade de troca de informações entre processadores diferentes, que é ordens de magnitude mais lenta quando realizada através de uma rede de comunicação.

Devido a isto, normalmente é possível executar um programa que foi desenvolvido segundo o modelo de memória distribuída em um computador de memória compartilhada sem perda de eficiência, pois o custo da comunicação entre processos provavelmente será muito baixo. O reverso tipicamente não é verdade. Programas desenvolvidos segundo o modelo de memória compartilhada *não podem* ser executados de forma eficiente em um computador de memória distribuída, porque a troca de informações, que teria um custo equivalente a escrita e leitura na memória, torna-se muito cara.

### Alocação

Para que um programa paralelo seja executado em um computador paralelo, cada processo deve ser atribuído a um processador e a cada processador, só um processo deve ser atribuído. O gerenciador de recursos faz esta atribuição de processos a processadores.  Você deve ter em mente que, se o seu programa segue o modelo de memória compartilhada, você  necessariamente precisa que todos os processadores alocados ao seu programa sejam localizados no mesmo nó. Se o seu programa segue o modelo de memória distribuída, esta restrição não existe, porém, como a comunicação intra nó é muito mais rápida do que a comunicação inter nós, ter  os processadores alocados no menor número de nós possível é desejável.

Você não deve tentar forçar a alocação de processadores no mesmo nó caso isto não seja necessário, no entanto, porque é possível que o número de processos que você requisitou  não esteja livre em um único nó, mas esteja disponível em um conjunto de nós. Neste caso, o seu programa irá para a fila e ficará aguardando, mesmo havendo processadores suficientes disponíveis no sistema.

Depois de toda esta elocubração abstrata, vamos examinar os  mínimos [comandos](commands) necessários para executar programas paralelos no cluster Zumbi.
