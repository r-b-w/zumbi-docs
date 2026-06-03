---
draft: true
title: 'VS Code acesso remoto'
weight: 10
cascade:
    type: docs
---

Você deve ter instalado o VS Code no computador que você usa para acessar o Zumbi, aquele na frente do qual você está sentado, seja ele Windows ou Linux, há apenas alguns detalhes diferentes na configuração, o uso do editor é o mesmo. Caso você esteja no Windows e usando um cliente gráfico de ssh no Windows como BitVise recomendado anteriormente, você precisa configurar também o [cliente nativo do Windows](./ssh)


Abra o VS Code e clique no Gerenciador de Extensões, ou clique `<Ctrl-Shift-x>`.

{{< figure
src="/images/vscode_extension.png"
alt="VS Code extensions"
caption="VS Code extensions"
width="60%"
>}}

Digite "ssh" no campo de busca das extensões, e mande instalar os dois que estão indicados. Certifique-se que o fornecedor seja a Microsoft. No meu caso a do meio veio junto automaticamente, o que não é problema. Depois de instaladas, as extensões devem estar disponíveis imediatamente, mas caso isto não ocorre, feche e abra o VS Code. 

Está longe de ser a pior ideia do mundo abrir o gerenciador de extensões novamente e ler a documentação destas extensões.

Clique no ícone indicado abaixo para iniciar uma nova conexão remota. O sua barra do VS Code deve ser bem diferente da minha, dependendo do que você tenha instalado, então procure por aquele ícone especificamente.

{{< figure
src="/images/vscode_open_connection.png"
alt="Nova conexão"
caption="Nova conexão"
width="60%"
>}}

Depois de clicar no ícone, vai aparecer o painel mostrado a seguir, no qual você deve selecionar "Connect to Host". No meu caso já aparecem vários hosts configurados, pois eu já tenho um arquivo de configuração para o ssh. No seu caso, muito provavelmente, não há nenhum.

{{< figure
src="/images/vscode_connect_to_host.png"
alt="Requisitar conexão remota"
caption="Requisitar conexão remota"
width="60%"
>}}

Vai aparecer um painel no qual você deve escolher "Adicionar Novo Host SSH"

{{< figure
src="/images/vscode_new_host.png"
alt="Adicionar novo host"
caption="Adicionar novo host"
width="60%"
>}}

Aqui vale a pena dividir temporariamente em duas trilhas separadas, para o Windows e para o Linux.

## Windows
No campo de entrada que aparece, digite o comando que você para conectar via ssh no Zumbi.

{{< figure
src="/images/ssh_conn_win.png"
alt="Configuração do novo host"
caption="Configuração do novo host"
width="90%"
>}}

Vou repetir o comando aqui pela legibilidade, e para facilitar o corta e cola.
```console
ssh -i c:/Users/ramir/.ssh/zumbi_pri_key ramiro@zumbi.padmec.org
```
Note que usamos as barras do Linux e não do Windows, mesmo estando no Windows. Se você usar as do Windows, não funciona. Você precisa adaptar o comando usando o seu nome de usuário no windows, no lugar de "ramir" (sim, está faltando um "o", é assim mesmo) e seu nome de usuário no Zumbi no lugar de "ramiro". Após estar muito certo que o seu comando está correto (você certamente deveria testá-lo no PowerShell antes de tentar usá-lo aqui), acione a tecla `<Enter>`


Um painel que pergunta qual arquivo de configuração do ssh que deve ser atualizado aparece. Escolha o que corresponde ao seu usuário, trocando "ramir" pelo seu nome de usuário no Windows. O arquivo vai ser modificado, pense bem no que você está fazendo. Acione a tecla  `<Enter>`

{{< figure
src="/images/vscode_win_config.png"
alt="Arquivo de configuração ssh"
caption="Arquivo de configuração ssh"
width="90%"
>}}

Se tudo der certo, um painel com a mensagem "Host Adicionado" será mostrado brevemente. Foi muito rápido e eu não consegui capturar. Agora você pode novamente clicar no ícone de conexão remota, selecional novamente "Connect to Host", e deve aparecer um painel com "zumbi.padmec.org". Clique nesta opção.

{{< figure
src="/images/vscode_ssh_zumbi.png"
alt="Host Zumbi"
caption="Host Zumbi"
width="90%"
>}}

Muitas coisas vão acontecer, coisas vão ser descarregadas, mas, se tudo der certo, uma nova janela do VS Code vai ser aberta, com um painel que pergunta a sua frase secreta (isto pode não acontecer se você já tiver o ssh-agent sendo executado). Informe frase secreta e tecle `<Enter>`

{{< figure
src="/images/vscode_enter_pf.png"
alt="Frase secreta"
caption="Frase secreta"
width="90%"
>}}

Quando você conseguir entrar com a sua frase secreta corretamente, a conexão será estabelecida, como indicado no canto inferior esquerdo do VS Code.
{{< figure
src="/images/vscode_stab_connect.png"
alt="Conexão estabelecida"
caption="Conexão estabelecida"
width="90%"
>}}

Se você clicar no explorador de arquivos do VS Code, você vai explorar os arquivos *no Zumbi*. Você pode mandar o VS Code abrir a sua pasta de login no Zumbi. Se você tiver configurado o ssh-agent, prova, e aí você terá acesso a todos os seus arquivos no Zumbi, para edição e manipulação.


## Linux
No campo de entrada que aparece, digite o comando que você para conectar via ssh no Zumbi.
No meu caso o comando é muito simples, pode ser mais sofisticado no seu. Esta parte é mais simples do que para Windows, pois, em princípio, já temos isto configurado corretamente [anteriormente](/ssh/connection). Após configurar o comando, digite `<Enter>`.

{{< figure
src="/images/vscode_ssh_cmd.png"
alt="Configuração do novo host"
caption="Configuração do novo host"
width="90%"
>}}


Um painel que pergunta qual arquivo de configuração do ssh que deve ser atualizado aparece. Escolha o que corresponde ao seu usuário. O arquivo vai ser modificado, pense bem no que você está fazendo. Digite `<Enter>`
{{< figure
src="/images/vscode_ssh_config.png"
alt="Arquivo de configuração ssh"
caption="Arquivo de configuração ssh"
width="90%"
>}}

Se tudo der certo, um painel com a mensagem "Host Adicionado" será mostrado brevemente. Foi muito rápido e eu não consegui capturar. Agora você pode novamente clicar no ícone de conexão remota, selecional novamente "Connect to Host", e deve aparecer um painel com "zumbi.padmec.org". Selecione esta opção e digite enter.
{{< figure
src="/images/vscode_ssh_zumbi.png"
alt="Host Zumbi"
caption="Host Zumbi"
width="60%"
>}}

Se o seu ssh estiver configurado para conexão sem senha, então muitas coisas vão acontecer, muitas mensagens vão impressas, muitas coisas vão ser descarregadas, mas uma hora vai aparecer uma nova janela do VS Code. Esta janela está conectada ao computador remoto conforme indicado no canto inferior esquerdo da nova janela do VS Code.

{{< figure
src="/images/vscode_stab_connect.png"
alt="Conexão estabelecida"
caption="Conexão estabelecida"
width="90%"
>}}

O que você editar nesta janela será feito no sistema de arquivos do Zumbi. Você pode mandar o explorar de arquivos do VS Code abrir a sua pasta raiz, e depois de confirmar que confia em tudo, terá acesso a todos os seus arquivos.

# scp

#  rsync
