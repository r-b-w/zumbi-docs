---
draft: false
title: 'Windows'
weight: 30
cascade:
    type: docs
---

Vamos tratar aqui apenas do cliente ssh [Bitvise](https://bitvise.com/ssh-client), cuja instalação foi sugerida anteriormente na etapa de [criação de chaves](/ssh/keys/windows/). Um procedimento equivalente deve ser usado para outros clientes. Vamos supor também que você não tenha usado o seu cliente ssh Bitvise para nenhuma outra conexão ainda, que você já criou seu par de chaves, já enviou a sua chave pública para nós e que sua conta já foi aberta!

Abra o seu client ssh Bitvise. Deve aparecer a janela abaixo. Se já houver algum campo preenchido, apague-o ou escreva por cima dele, até que a janela corresponda à que é mostrada ao final desta seção. Não esqueça de substituir o seu próprio nome de usuário e qualquer outra coisa específica sua.

{{< figure
src="bitvise_initial.png"
alt="Janela inicial Bitvise"
caption="Janela inicial Bitvise"
width="85%"
>}}

Preencha o nome do computador com o qual você quer estabelecer a comunicação, no caso, zumbi.padmec.org, 
{{< figure
src="bitvise_host.png"
alt="Escolha do endereço do servidor"
caption="Endereço do cluster Zumbi"
width="85%"
>}}

Entre com o seu nome de usuário (não o meu), que terá sido fornecido por nós,
{{< figure
src="bitvise_username.png"
alt="Entrada do nome de usuário"
caption="Entrada do nome de usuário"
width="85%"
>}}

No campo `initial_method`, escolha `public_key`,
{{< figure
src="bitvise_public.png"
alt="Escolha do tipo de autenticação"
caption="Autenticação por chave privada"
width="85%"
>}}

Ao fazer isto, campos para escolher a sua chave privada e digitar a frase secreta desta chave vão aparecer. Você pode deixar no automático, ou pode já escolher a chave privada correspondente à chave pública que você enviou. Para escolher a chave, clique em `Client key`, e deve aparecer um painel como este, 
no qual você clica na chave correta, como indicado,
{{< figure
src="bitvise_choosekey.png"
alt="Escolha da chave privada"
caption="Escolha da chave privada"
width="85%"
>}}

Clicando em `OK` com a chave privada selecionada, você deve retornar para o painel de login, e você pode digitar a frase secreta desta chave no campo `Passphrase`.
{{< figure
src="bitvise_passphrase.png"
alt="Entrada da chave secreta"
caption="Entrada da chave secreta"
width="85%"
>}}

Certifique-se que o painel de login corresponde ao mostrado acima, a menos do nome de usuário e mensagens de log do programa, e clique em `Log in`. Aguarde alguns instantes. Caso seja a primeira vez que você está tentando a conexão com este servidor, deve aparecer um aviso de segurança, isto é normal. Clique em `Accept and Save`
{{< figure
src="bitvise_accept.png"
alt="Aceitando host remoto"
caption="Aceitando host remoto"
width="85%"
>}}

Aguarde um pouco. Muitas mensagens serão geradas na janela de registros, mas não deve aparecer nenhum erro. Caso sua tentativa de conexão tenha sucesso, deve aparecer uma janela como a seguinte. Perceba que novas opções apareceram na coluna de comandos. Para verificar se tudo deu certo mesmo, clique em `New terminal console`,
{{< figure
src="bitvise_connection.png"
alt="Abertura de terminal"
caption="Abertura de terminal"
width="85%"
>}}

Deve aparecer um emulador de terminal no qual você poderá digitar comandos, como um homem das cavernas, como mostrado abaixo. Parabéns, você está conectado ao nó de login do Zumbi. Digite alguns comandos do Linux, se for do seu interesse, para ver se está tudo funcionando. Para desconectar-se você pode simplesmente fechar a janela, ou digitar `logout`, ou `<Ctrl>-d` (isto é, pressione a tecla `Ctrl` e a tecla `d` ao mesmo tempo).
{{< figure
src="bitvise_console.png"
alt="Console"
caption="Console"
width="100%"
>}}

Este programa permite que você faça muitas coisas úteis, como transferir arquivos do seu computador para o Zumbi e vice-versa, criar "tuneis" para fazer uma conexão de rede remota parecer local, e vice-versa, e até mesmo criar um desktop gráfico. Antes destas novas aventuras é conveniente que você entenda um pouco sobre como o [armazenamento de arquivos](/storage) é feito no Zumbi. 
