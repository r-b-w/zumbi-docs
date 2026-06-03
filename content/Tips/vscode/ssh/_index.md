---
draft: true
title: 'Cliente nativo'
weight: 10
cascade:
    type: docs
---

Vamos supor que você já configurou o [BitVise clienti](ssh/keys/windows). Precisamos agora exportar a chave privada, com todas as ressalvas que fizemos sobre a sua segurança. 

Abra o BitVise client e escolha o gerenciador de chaves.
{{< figure
src="/images/bv_key_manager.png"
alt="Cliente ssh BitVise"
caption="Cliente ssh BitVise"
width="60%"
>}}

Na janela do gerenciador de chaves, escolha a que você usou para exportar a chave pública que foi  usada para abrir sua conta no Zumbi. Os números que vão aparecer na sua janela *tem que ser* completamente diferentes daqueles mostrados aqui! Não se preocupe com isto! Clique na setinha ao lado de "Export".

{{< figure
src="/images/bv_zumbi_key.png"
alt="Escolha do par de chaves"
caption="Escolha do par de chaves"
width="80%"
>}}


Escolha as opções mostradas "Export private key" e "OpenSSH format", depois clique em "Export".
{{< figure
src="/images/bv_priv_key.png"
alt="Exportar chave privada"
caption="Exportar chave privada"
width="60%"
>}}

Um painel de opções para a frase de segurança aparece. Digite a frase de segurança no campo indicado, deixe marcado "Export using existing passphrase" e clique em "Continue".
{{< figure
src="/images/bv_passp.png"
alt="Frase de segurança"
caption="Frase de segurança"
width="60%"
>}}

Será aberta uma janela de gerenciamento de aquivos onde você deve escolher o destino da chave privada. É *muito importante* que você tenha sua janela configurada para exibir arquivos ocultos, que começam com um ponto, caso contrário você nunca vai encontrar o diretório no qual deve salvar a chave. Estamos usando o diretório de configuração do ssh do seu usuário, que tipicamente é "C:\Usuários\ramir\.ssh", onde "ramir" é o nome do meu usuário neste computador. Sim digitei errado e fiquei com preguiça de arrumar depois. Eu escolhi o nome  "zumbi_priv_key", você pode usar o que quiser.

{{< figure
src="/images/bv_save_key.png"
alt="Arquivo de destino"
caption="Arquivo de destino"
width="80%"
>}}


Sendo completamente honesto, você pode salvar este arquivo  onde quiser, desde que seja um diretório seguro, no qual somente você possa ler e escrever, e que você se lembre onde foi depois.

Depois de salvar a chave, você pode fechar o cliente BitVise. Para ver se tudo deu certo, vamos tentar uma conexão com o cliente nativo. Abra o PowerShell e digite o comando mostrado abaixo.

```console
O Windows PowerShell
Copyright (C) Microsoft Corporation. Todos os direitos reservados.

Instale o PowerShell mais recente para obter novos recursos e aprimoramentos! https://aka.ms/PSWindows

PS C:\WINDOWS\system32> cd c:\Users\ramir
PS C:\Users\ramir> ssh -i .\.ssh\zumbi_priv_key ramiro@zumbi.padmec.org
Enter passphrase for key '.\.ssh\zumbi_priv_key':
Web console: https://zumbi:9090/ or https://192.168.100.10:9090/

Last login: Wed Jun  3 18:04:00 2026 fro-m 150.161.138.218
[ramiro@zumbi ~]$
````
Você tem que adaptar este teste ao seu sistema, trocando "ramir" pelo seu nome de usuário no Windows, e "ramiro" por seu nome de usuário no Zumbi. E é claro mais qualquer coisa que você tenha improvisado.

Pronto temos um glorioso terminal gráfico no Linux, que podemos usar como um adulto. No momento não vamos usá-lo para nada.

## ssh-agent
E muito conveniente configurar o `ssh-agent` no Windows, para evitar que você fique digitando sua frase secreta toda a hora, se estiver usando o console. 

Você precisa iniciar um PowerShell **rodando como administrador**. Use o botão direito do mouse e escolha "Executar como administrador".

Neste PowerShell digite os comandos mostrados a seguir.

```console
O  Windows PowerShell
Copyright (C) Microsoft Corporation.
Todos os direitos reservados.
Instale o PowerShell mais recente para obter novos recursos e aprimoramentos! https://aka.ms/PSWindows
PS C:\WINDOWS\system32> Set-Service -Name ssh-agent -StartupType Automatic
PS C:\WINDOWS\system32> Start-Service ssh-agent
PS C:\WINDOWS\system32> Get-Service ssh-agent

Status   Name DisplayName
------   ----               -----------
Running  ssh-agent          OpenSSH Authentication Agent
PS C:\WINDOWS\system32>
```

Feche *imediatamente* este shell para minimizar o risco de acidentes.

Como o seu *usuário normal do Windows*, abra outro PowerShell, sem permissões de administrador e carregue a sua chave privada.

```console
O Windows PowerShell
Copyright (C) Microsoft Corporation. Todos os direitos reservados.

Instale o PowerShell mais recente para obter novos recursos e aprimoramentos! https://aka.ms/PSWindows

PS C:\WINDOWS\system32> cd c:\Users\ramir\
PS C:\Users\ramir> ssh-add .ssh/zumbi_priv_key
Enter passphrase for .ssh/zumbi_priv_key:
Identity added: .ssh/zumbi_priv_key (.ssh/zumbi_priv_key)
PS C:\Users\ramir>
```

A partir de agora, você pode usar o ssh sem digitar a sua frase secreta, dentro deste shell e seus descendentes.

```console
PS C:\Users\ramir> ssh ramiro@zumbi.padmec.org hostname
zumbi
PS C:\Users\ramir> ssh ramiro@zumbi.padmec.org pwd
/home/ramiro
PS C:\Users\ramir> ssh ramiro@zumbi.padmec.org date
qua 03 jun 2026 19:35:24 -03
PS C:\Users\ramir> ssh ramiro@zumbi.padmec.org whoami
ramiro
PS C:\Users\ramir>
```

