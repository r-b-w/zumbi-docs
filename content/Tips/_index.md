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

# scp

Se você acessa o Zumbi usando um cliente ssh gráfico, a partir do Windows, muito provavelmente já tem alguma forma de transferir arquivos do seu computador para o Zumbi. 

Caso você esteja usando um cliente de linha de comando, a principal forma de transferir arquivos é o programa [`scp`](https://man7.org/linux/man-pages/man1/scp.1.html), disponível em praticamente todos os sistemas nos quais você tem o comando `ssh` instalado. Como usual, vamos supor que você tenha o `ssh-agent` ativado e com a sua chave privada carregada, de forma que você possa fazer o login via ssh sem digitar sua frase secreta, seja no [Windows](http://localhost:1313/tips/vscode/ssh/#ssh-agent) ou no [Linux](http://localhost:1313/ssh/connection/linux/#ssh-agent).

A sintaxe formal do comando ssh é `scp user1@host1:arquivo1 user2@host2:arquivo2`, onde 1 representa o sistema de origem, e 2 representa o sistema de destino. A maioria destes campos pode ser omitida, porque os defaults normalmente fazem muito sentido. Vamos ver alguns exemplos.

Para copiar um arquivo do seu diretório raiz, do seu computador cliente, para o seu diretório raiz no Zumbi, supondo que você tenha o mesmo nome nos dois sistemas, basta fazer

```console
ramiro@wenovo:~$ scp p1.pdf zumbi.padmec.org:
p1.pdf                                                                 100%  122KB  70.1KB/s   00:01
ramiro@wenovo:~$ 
```
Não foi necessário especificar o host de origem, porque é seu próprio computador, nem os nomes de usuários, nem o nome de arquivo no destino, já que neste caso o `scp` usa o mesmo nome.

No sentido reverso, com as mesmas hipóteses,
```console
ramiro@wenovo:~$ scp zumbi.padmec.org:p1.pdf .
p1.pdf                                                                100%  122KB  30.5KB/s   00:04    
ramiro@wenovo:~$ 
```
Neste caso, o '.' indica o diretório no qual você está executando o `scp`. O computador de destino não foi especificado, então é o seu próprio computador, e os nomes de usuário foram omitidos porque são os mesmos. 

Vamos repetir o primeiro comando, supondo que o nome de usuário no Zumbi seja diferente do seu nome de usuário local, e que você queira transferir o arquivo para um subdiretório do seu diretório raiz no Zumbi, *que já deve existir*, mudando o nome do arquivo no processo

```console
ramiro@wenovo:~$ scp p1.pdf ramiro@zumbi.padmec.org:data/delete_me.pdf
p1.pdf                                                                100%  122KB  74.8KB/s   00:01    
ramiro@wenovo:~$
```

O `scp` pode ser usado para transferir vários arquivos de uma vez, com uma pequena variação na sintaxe, `scp -r lista_de_arquivos_1 diretório_2`, onde 1 e 2 são a origem e destino, respectivamente, e a notação para hosts e usuários é a mesma que para arquivos individuais. Por exemplo,
```console
ramiro@wenovo:~$ scp -r tmp  zumbi.padmec.org:data/delete_me_dir
fig_01.png                                                           100%  131KB  85.4KB/s   00:01    
file01.png                                                           100%  500KB 187.8KB/s   00:02   
logo_coopfisio.png                                                   100%   93KB 252.4KB/s   00:00    
ramiro@wenovo:~$ 
```
O diretório de destino não precisa existir, mas todos os seus "ancestrais" devem existir antes da transferência. Neste caso, "tmp" é um diretório com três arquivos. Podemos verificar o resultado usando o comando `ssh`, que pode ser usado para executar comandos no sistema remoto.
```console
ramiro@wenovo:~$ ssh zumbi.padmec.org ls -l data/delete_me_dir
total 728
-rw-r--r-- 1 ramiro ramiro 134401 jun  5 15:19 fig_01.png
-rw-r--r-- 1 ramiro ramiro 511601 jun  5 15:19 file01.png
-rw-r--r-- 1 ramiro ramiro  95530 jun  5 15:19 logo_coopfisio.png
ramiro@wenovo:~$
```
Perceba que só usamos ":" quando estamos descrevendo um arquivo ou diretório, o nome do host não tem ":". Podemos inclusive remover este diretório remoto a partir do seu próprio computador. Faça este tipo de operação com *extremo cuidado*. O ssh vai usar o comando `rm` padrão do sistema, que não confirma nada, só obedece cegamente.


```console
ramiro@wenovo:~$ ssh zumbi.padmec.org rm -r data/delete_me_dir
ramiro@wenovo:~$ ssh zumbi.padmec.org ls data
delete_me.pdf
miniforge3
opt
spack-local
var
work
ramiro@wenovo:~$ ssh zumbi.padmec.org rm data/delete_me.pdf
ramiro@wenovo:~$ ssh zumbi.padmec.org ls data
miniforge3
opt
spack-local
var
work
ramiro@wenovo:~$
```

Se você quiser transferir um grande volume de dados, no entanto, você deve usar o `rsync` ao invés do `scp`.

# rsync
O [`rsync`](https://rsync.samba.org/) é um programa feito para sincronizar, isto é, tornar idênticos, dois diretórios, que tipicamente estão em computadores diferentes. A maior diferença entre o `rsync` e `scp` é que, ao copiar um diretório, o `rsync` só transmite *as diferenças* entre os dois diretórios. Se você tem dois diretórios com centenas de megabytes de arquivos, mas apenas um deles tem uma linha de diferença entre os dois sistemas, o `rsync` vai transmitir apenas esta diferença, o que pode ser ordens de magnitude mais rápido.

O `rsync` é um programa muito completo e complexo, e você realmente vai lucrar muito se der uma lida na sua documentação, mas vamos mostrar alguns exemplos iniciais. A origem e o destino usam basicamente a mesma notação que o `scp`. Vamos primeiro copiar um diretório inteiro do meu computador para o Zumbi. O primeiro comando é apenas para saber o tamanho total do que vais ser transferido, não é estritamente necessário.


```console
ramiro@wenovo:~/Sync/Study/OpenFOAM$ du -sh cfd_chalmers/
1,1M	cfd_chalmers/
ramiro@wenovo:~/Sync/Study/OpenFOAM$ rsync -rv cfd_chalmers zumbi.padmec.org:
sending incremental file list
cfd_chalmers/
cfd_chalmers/cavityFourCells/
cfd_chalmers/cavityFourCells/0/
cfd_chalmers/cavityFourCells/0/U
cfd_chalmers/thermalSquare/constant/polyMesh/points
#
# many lines elided
#
cfd_chalmers/thermalSquare/system/
cfd_chalmers/thermalSquare/system/blockMeshDict
cfd_chalmers/thermalSquare/system/controlDict
cfd_chalmers/thermalSquare/system/fvSchemes
cfd_chalmers/thermalSquare/system/fvSolution

sent 898.290 bytes  received 1.090 bytes  51.393,14 bytes/sec
total size is 893.940  speedup is 0,99
ramiro@wenovo:~/Sync/Study/OpenFOAM$ 
```

Vamos ver o que acontece se eu refizer a mesma operação agora.

```console
ramiro@wenovo:~/Sync/Study/OpenFOAM$ rsync -rv cfd_chalmers zumbi.padmec.org:
sending incremental file list
cfd_chalmers/cavityFourCells/0/U
cfd_chalmers/cavityFourCells/0/p
#
# .....
#
cfd_chalmers/thermalSquare/system/fvSchemes
cfd_chalmers/thermalSquare/system/fvSolution

sent 8.992 bytes  received 8.642 bytes  1.137,68 bytes/sec
total size is 893.940  speedup is 50,69
ramiro@wenovo:~/Sync/Study/OpenFOAM$ 
```
Note que foram transmitidos apenas 8k bytes, ao invés de praticamente 900k. É claro que alguma informação tem que ser trocada para que o programa saiba que os dois lados são idênticos.

Eu vou fazer uma mudança de 1 caractere no arquivo "cfd_chalmers/myThermalConductionSolver/myThermalConductionSolver.C". 
Ela passou de 
```
You should have received a copy of the GNU General Public License
```
para
``` 
xou should have received a copy of the GNU General Public License
```
Se eu sincronizar os diretórios novamente,
```console
ramiro@wenovo:~/Sync/Study/OpenFOAM$ rsync -rv cfd_chalmers zumbi.padmec.org:
sending incremental file list
cfd_chalmers/cavityFourCells/0/U
cfd_chalmers/cavityFourCells/0/p
cfd_chalmers/cavityFourCells/constant/physicalProperties
......
cfd_chalmers/thermalSquare/system/fvSchemes
cfd_chalmers/thermalSquare/system/fvSolution

sent 9.692 bytes  received 8.642 bytes  1.264,41 bytes/sec
total size is 893.940  speedup is 48,76
ramiro@wenovo:~/Sync/Study/OpenFOAM$ 
```
Transferimos mais ou menos 1K a mais do que o mínimo necessário, em um diretório com mais ou menos um megabyte de dados, para um ganho de quase 50 vezes. Quando maior o tamanho do diretório, maior é a economia. Se conferíssemos o arquivo remoto agora, ele estaria igual ao arquivo local novamente.

O `rsync` pode ser usado para fazer backups incrementais, pode usar listas de exclusão e inclusão e muito mais coisas, é uma das ferramentas mais úteis para quem trabalha com arquivos remotos.

# dolphin 
O Dolphin é o gerenciador de arquivos da ambiente de trabalho KDE para o Linux. Acredito que alguns outros gerenciadores de arquivos de outros sistemas tenham funcionalidade semelhante, mas não os conheço. 

Você pode usar o Dolphin para gerenciar seus arquivos remotos, desde que, novamente, já tenha o seu sistema configurado para o [login sem frase secreta](http://localhost:1313/ssh/connection/linux/#ssh-agent). O que você precisa fazer depende um pouco de como o seu sistema está configurado. O KDE é muito flexível e diferentes distribuições tem configurações padrão diferentes.

Abrindo uma janela do Dolphin, temos algo como o mostrado a seguir. Clique no campo no qual o diretório atual é mostrado, onde está escrito "Início", com o botão esquerdo do mouse.
{{< figure
src="/images/dolphin_00.png"
alt="Dolphin"
caption="Dolphin"
width="80%"
>}}

O resultado deve ser análogo a este, onde agora você pode digitar um caminho de destino neste campo. Deve estar sendo listado o seu diretório atual, provavelmente o seu diretório raiz. Apague o diretório que estiver lá e digite "fish://ramiro@zumbi.padmec.org/home/ramiro", obviamente, trocando "ramiro" por seu nome de usuário no Zumbi. Tecle `<Enter>` com o cursor dentro deste campo ainda.
{{< figure
src="/images/dolphin_10.png"
alt="Endereço remoto"
caption="Endereço remoto"
width="80%"
>}}

Depois de algumas maquinações, a janela deve mudar, magicamente, para mostrar os seus arquivos no Zumbi.

{{< figure
src="/images/dolphin_20.png"
alt="Arquivos remotos"
caption="Arquivos remotos"
width="80%"
>}}

Você pode copiar e mover os arquivos desta janela para outra janela ou outra aba do Dolphin exatamente como se fossem arquivos locais. É possível também simplesmente dividir este painel e clicar em qualquer outro diretório na coluna de locais à esquerda, e fazer todas as operações na mesma janela.
{{< figure
src="/images/dolphin_30.png"
alt="Arquivos remotos e locais"
caption="Arquivos remotos e locais"
width="80%"
>}}

Eu mudei um pouco a apresentação dos ícones para caber mais informação na janela. Basta agora clicar e arrastar um ícone de uma janela para a outra para movê-lo ou copiá-lo. Você também pode clicar em um arquivo remoto e abri-lo com um editor local, como o Kate, por exemplo, que é um editor de programação decente. Eu tenho quase certeza que o arquivo é copiado de um sistema para o outro para a edição, e de volta quando você o salva, então isto não é muito eficiente. Eu recomendo usar o [VS Code](vscode).

