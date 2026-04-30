---
draft: true
title: 'Linux'
weight: 20
cascade:
    type: docs
---

Estas instruções também funcionam para o Windows subsystem for Linux, [WSL 2](https://learn.microsoft.com/en-us/windows/wsl/install), que, provavelmente, é o que você deveria estar usando se você realmente pretende se envolver com computação de alto desempenho. Muito provavelmente funcionam com o [Cygwin](https://www.cygwin.com/) também. Vamos supor que você tenha uma instalação do Linux funcionando e tenha um mínimo de conforto com o uso da linha de comando.

No Linux, tipicamente, usa-se o [OpenSSH](https://www.openssh.org/) para implementar o protocolo secure shell. O OpenSSH fornece um cliente, um servidor e vários utilitários. Vamos usar o utilitário `ssh-keygen` para gerar o par de chaves.

A maneira mais fácil de gerar o par, ideal para quando você não sabe bem o que está fazendo *tem certeza* que nunca gerou nenhum par antes é simplesmente rodar o programa

```bash
ramiro@rbw:tmp$ ssh-keygen
Generating public/private ed25519 key pair.
Enter file in which to save the key (/home/ramiro/.ssh/id_ed25519): 
```

Neste ponto você pode apenas digitar `enter` para aceitar o nome padrão, ou entrar com outro nome no console. Vamos continuar com o jeito mais fácil possível. Serão gerados dois arquivos:
- `id_ed25519`: chave privada
- `id_ed25519.pub`: chave pública

O padrão é que estes arquivos sejam gerados no diretório padrão da configuração do ssh, `/home/ramiro/.ssh`, no meu caso. Claramente, substitua `ramiro` pelo seu nome de usuário no seu próprio computador.

Ao digitar `enter` no prompt anterior, temos que informar o passphrase. **Não digite apenas `enter` aqui**. Escolha uma frase de segurança diferente daquele exemplo acima, por favor!

```bash
ramiro@rbw:tmp$ ssh-keygen -t ed25519
Generating public/private ed25519 key pair.
Enter file in which to save the key (/home/ramiro/.ssh/id_ed25519): 
Enter passphrase (empty for no passphrase):
```

Após entrar com a frase de segurança e digitar `enter`, você precisa repeti-la, é claro, e após você conseguir digitar a mesma frase corretamente, às cegas, duas vezes consecutivas, o seu par de chaves é gerado.

```bash
ramiro@rbw:tmp$ ssh-keygen
Generating public/private ed25519 key pair.
Enter file in which to save the key (/home/ramiro/.ssh/id_ed25519): 
Enter passphrase (empty for no passphrase):
Enter same passphrase again: 
Your identification has been saved in /home/ramiro/.ssh/id_ed25519
Your public key has been saved in /home/ramiro/.ssh/id_ed25519.pub
The key fingerprint is:
SHA256:9AYTu2qHbRQHn0xCdvNrbR+t1iPKMYN6FKDdN0I2Tqc ramiro@rbw
The key's randomart image is:
+--[ED25519 256]--+
|       .* +      |
|       o # =     |
|      o & O .    |
|     . o E o o  .|
|        S * + o..|
|       = o.. . +.|
|      + =. + .o.o|
|     . o... =.. .|
|       ..  o     |
+----[SHA256]-----+
ramiro@rbw:tmp$
```

Perceba que o sistema informa onde foram salvas as chaves pública e privada. Se você não sabe o que está fazendo nunca mova, copie ou modifique de forma alguma a sua chave privada. Com a chave pública você pode fazer qualquer coisa, inclusive anexá-la a um e-mail e enviá-la para nós para que possamos abrir uma conta para você.

Para reforçar, mande para nós o arquivo `/home/ramiro/.ssh/id_ed25519.pub`, com o seu nome de usuário substituído no lugar de `ramiro`. Não mexa em mais nada do diretório `.ssh`.

É claro que você pode escolher salvar a chave com outro nome, ou guardá-la em outro diretório. Estamos considerando que neste caso você tem alguma noção do que está fazendo e não precisa tanto de alguém que segure a sua mão. Você pode agora tentar [logar no Zumbi](/ssh/connection/linux).
