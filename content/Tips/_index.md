---
draft: true
title: 'Dicas'
weight: 50
cascade:
    type: docs
---

Se você é um usuário experiente de Linux, pode parar de ler por aqui.

Aqui serão listadas algumas dicas que podem facilitar o uso do Zumbi se você não está muito familiarizado com o Linux. 

# MC
Se você tem mais de 50 anos e usou computadores quando jovem, deve estar familiarizado com o programa [Norton Commander](https://en-wikipedia-org.translate.goog/wiki/Norton_Commander?_x_tr_sl=en&_x_tr_tl=pt&_x_tr_hl=pt&_x_tr_pto=tcr), com o qual você podia fazer a gerência de aquivos no DOS, de modo "gráfico". 

Existe até hoje um clone deste programa, chamado [MC](https://midnight-commander.org/), que funciona em todos os sistemas operacionais que tenham um terminal. Para acessá-lo, basta digitar `mc` na linha de comando do terminal, e vai aparecer a janela abaixo. Para sair do programa, digite
`<F10>` ou `<Esc-0>`.

{{< figure
src="/images/mc.png"
alt="Programa MC"
caption="Programa MC"
width="85%"
>}}

Você navega pelo sistema de arquivos com as teclas de setas de direção, e pode acessar o menu superior com `<F9>`, e em seguida use as teclas de navegação para escolher um item. Use a tecla `<Tab>` para mudar de painel. O uso deste programa deveria sem bem intuitivo. Em muitos sistemas até o mouse funciona. Este programa pode ser muito útil para segurar a sua mãozinha até que você se familiarize o suficiente com os comandos do terminal. Por exemplo, veja o que você pode fazer com o menu "File"

{{< figure
src="/images/mc_file.png"
alt="Comandos de arquivos"
caption="Comandos de arquivos"
width="40%"
>}}

Você pode usá-lo para copiar, mover, remover, examinar e até editar arquivos. Clicando em `<F4>`, você abre o arquivo selecionado em um editor bem simples, mas que é o suficiente para pequenos consertos e atualizações de arquivos de configuração, como mostrado muitas vezes neste material.


# VS Code
O melhor editor de programação que existe é o [neovim](https://neovim.io/), mas se você não sabe isto ainda, eu não espero que você acredite.

Pessoas normais provavelmente devem usar o [Visual Studio Code](https://code.visualstudio.com/). O VS Code *não está* instalado no Zumbi, e *não deve ser*. O VS Code pode funcionar para editar arquivos remotamente, em um modo cliente servidor. Vamos consider aqui que você tem o seu sistema configurado para [acessar o Zumbi via ssh](/ssh/connection), muito preferencialmente usando alguma espécie de agente que permite a conexão direta sem senhas. Para não carregar muito esta página, vamos examinar a configuração do VS Code para edição remota em uma [página separada](./vscode).


