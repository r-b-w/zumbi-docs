---
draft: false
title: 'tmux'
weight: 20
cascade:
    type: docs
---

Vamos logar no zumbi, e digitar `tmux` e `<Enter>` na linha de comando. Aparecerá a janela inicial do `tmux`, que aparentemente é um terminal normal que você pode usar como qualquer outro.
{{< figure
src="tmux_10.png"
alt="tmux"
caption="tmux"
width="80%"
>}}

Perceba, no entanto, que há uma linha de status diferente na base da janela. Esta linha de status é configurável (não vamos fazer isto, estude a documentação ou peça ajuda a uma IA). 

Todos os comandos do `tmux` começam com uma tecla de prefixo, configurável, cuja atribuição padrão é `<Ctrl-b>`. Por exemplo, para examinar a ajuda do `tmux`, digite `<Ctrl-b>` `<?>`, isto é aperte simultaneamente as teclas `<Ctrl>` e `<b>`, solte as duas e digite imediatamente a tecla `<?>`, o ponto de interrogação. Não é possível capturar este comando em uma imagem, as teclas de comando do `tmux` não tem feedback visual. Fazendo isto, aparece a lista de comandos do `tmux` com a atribuição de telas padrão. Você pode ir para a frente a para trás usando `<PgUp>` e `<PgDown>`, e sair da ajuda com `<q>`. Na versão mostrada há 58 comandos diferentes.
{{< figure
src="tmux_20.png"
alt="Help do tmux"
caption="Help do tmux"
width="80%"
>}}

Mesmo se você não fizer aprender e não fizer mais nada, o simples fato de estar dentro do `tmux` mantem sua sessão aberta, mesmo que você perca a conexão por algum motivo. Vamos abrir um programa dentro desta sessão.
{{< figure
src="tmux_30.png"
alt="mc em uma sessão do tmux"
caption="mc em uma sessão do tmux"
width="80%"
>}}

E agora vou "perder a conexão". Vocês tem que acreditar em mim, vou fechar esta janela clicando no botão de fechar a janela no meu computador local, sem salvar nada, sem fazer mais nada. É como se eu tivesse puxado o cabo azul do meu computador. Não dá para capturar a janela em uma imagem estática porque ela desapareceu.

Agora, só para enfeitar, a partir do Windows, vou fazer uma nova conexão ao Zumbi.
{{< figure
src="tmux_40.png"
alt="Nova conexão, a partir do Windows"
caption="Nova conexão, a partir do Windows"
width="80%"
>}}

Vamos verificar se existem sessões do  `tmux` ativas com `tmux list-sessions`, e depois de verificar que existe uma, restabelecer a conexão com ela, com `tmux attach`. Como só há uma sessão, não é necessário sequer especifica a qual você quer conectar-se, o que seria o caso se houvesse várias sessões ativas.
 
{{< figure
src="tmux_50.png"
alt="Lista de sessões e reconexão"
caption="Lista de sessões e reconexão"
width="80%"
>}}

Imediatamente após o comando  `tmux attach`, o terminal volta ao estado que estava antes da perda da conexão, com o mesmo programa aberto, o cursor na mesma posição e você pode continuar exatamente de onde parou!

{{< figure
src="tmux_60.png"
alt="Sessão restaurada"
caption="Sessão restaurada"
width="80%"
>}}

Isto pode não parecer muito impressionante hoje em dia, mas na época em que a Internet doméstica era feita por modens telefônicos e as conexões eram muito instáveis, programas como este eram o que mantinha a sanidade de quem acessava computadores remotamente.

O mais importante deste processo de desconexão e reconexão é que o programa continuou rodando o tempo todo. Como o `mc` mostrado neste exemplo não está fazendo nada, isto não é aparente, então vamos mostrar outro exemplo. Vamos sair do `mc`, apertando `<F10>`, e vamos rodar uma sequência de comandos que vai imprimir a hora a cada 10 segundos, até ser interrompida por `<Ctrl-c>`.  Após algumas impressões, eu vou fechar a janela, e vou retomar a conexão a partir do Linux.

{{< figure
src="sleep_10.png"
alt="sleep 10 para sempre"
caption="sleep 10 para sempre"
width="80%"
>}}

Vou abrir uma nova conexão a partir do Linux, e novamente digitar `tmux attach`.  O resultado é a janela mostrada a seguir.

{{< figure
src="sleep_20.png"
alt="sleep 10 para sempre, reconectada"
caption="sleep 10 para sempre, reconectada"
width="80%"
>}}

É óbvio que o programa continuou rodando, como se ainda estivesse conectado a um terminal físico. Preciso interromper o programa com `<Ctrl-c>` para retomar a interação com o terminal.

Outra característica muito útil do `tmux` criar várias telas virtuais em uma mesma sessão. Você cria uma nova tela virtual e automaticamente muda para ela com `<Ctrl-b>``<c>`, que digitei imediatamente depois de interromper o programa mostrado na figura anterior. O resultado é

{{< figure
src="vscreen_10.png"
alt="Nova tela virtual"
caption="Nova tela virtual"
width="80%"
>}}

Percebam que a barra de status mudou. Ela indica agora que há duas janelas virtuais, "0" e "1", as duas rodando o programa "bash", e a janela que está conectada ao terminal físico, indicada pelo "*", é a janela 1, recém criada. Você pode mudar diretamente para uma janela virtual específica digitando `<Ctrl-b>` `<#>`, onde "#" é o número da janela, ou digitando `<Ctrl-b>` `<n>` para ir para a próxima janela, e `<Ctrl-b>` `<p>`, para ir para a janela anterior.

Os programas nas janelas virtuais continuam rodando, mesmo quando não estão conectadas ao terminal físico. Apenas para ilustrar, vou abrir um monitor do sistema na janela 1, e um editor de texto na janela 2, e alternar entre elas. Vou digitar `atop` na janela 1, digitar `<Ctrl-b>` `<0>`, e digitar `nvim .bashrc` na janela 0. O resultado é 

{{< figure
src="vscreen_20.png"
alt="Editor de texto"
caption="Editor de texto"
width="80%"
>}}

e digitando `<Ctrl-b>` `<1>` ou `<Ctrl-b>` `<p>` ou `<Ctrl-b>` `<n>` (como só há duas janelas a anterior e a próxima são as mesmas),

{{< figure
src="vscreen_30.png"
alt="Monitor de desempenho"
caption="Monitor de desempenho"
width="80%"
>}}

Note que lina de status muda para refletir o que está sendo executado em cada janela. Você pode "matar" uma janela virtual com `<Ctrl-b>` `<k>`. Digitando `<Ctrl-b>` `<&>` na janela virtual 1, ficamos com


{{< figure
src="vscreen_40.png"
alt="Fechando janela virtual"
caption="Fechando janela virtual"
width="80%"
>}}

Ao confirmar com "y", a janela 1 e fechada e voltamos para a configuração com uma só janela, que, neste caso, exibirá a janela 0. 

{{< figure
src="vscreen_50.png"
alt="Fechando janela virtual"
caption="Fechando janela virtual"
width="80%"
>}}

Além de novas janelas virtuais, é possível dividir uma janela, verticalmente ou horizontalmente, em painéis diferentes. Digitando `<Ctrl-b>` `<">`, o painel atual é divido por uma linha horizontal,

{{< figure
src="split_10.png"
alt="Painéis empilhados verticalmente"
caption="Painéis empilhados verticalmente"
width="80%"
>}}

e digitando`<Ctrl-b>` `<%>`, dividido por uma linha vertica.
{{< figure
src="split_20.png"
alt="Painéis arranjados lateralmente"
caption="Painéis arranjados lateralmente"
width="80%"
>}}

Você pode usar `<Ctrl-b>` `<o>` para "circular" pelos painéis e `<Ctrl-b>` `<x>` para "matar" o painel que tem o foco atual. 

Há ainda comandos para cortar e colar texto entre painéis e janelas diferentes, aumentar e diminuir o tamanho dos painéis, mover painéis entre janelas e uma infinidade de coisas úteis. Mais uma vez, a documentação é sua aliada.


