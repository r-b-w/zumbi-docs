---
draft: true
title: 'Linux'
weight: 30
cascade:
    type: docs
---

Estas instruções provavelmente também aplicam-se, pelo menos até a parte do `ssh-add`, ao WSL2 e ao cygwin. Se você seguiu os procedimentos sugeridos anteriormente, seu par de chaves está em uma localização padrão, seu diretório de configuração do cliente ssh, que é, por default, o diretório `.ssh` no seu diretório raiz. 

No meu caso, por exemplo, tenho:
```console
ramiro@rbw:~$ ls -l /home/ramiro/.ssh/id_ed25519
-rw------- 1 ramiro ramiro 444 mar 26 16:30 /home/ramiro/.ssh/id_ed25519
ramiro@rbw:~$ ls -l /home/ramiro/.ssh/id_ed25519.pub 
-rw-r--r-- 1 ramiro ramiro 92 mar 26 16:30 /home/ramiro/.ssh/id_ed25519.pub
ramiro@rbw:~$ 
```

Aqui, `id_ed25519` é o arquivo com a chave privada, e `id_ed25519.pub` é o arquivo com a chave púbica. É claro que você terá que trocar `ramiro` pelo seu nome de usuário local, e, caso você tenha escolhido outros nomes e destinos para as suas chaves pública e privada, terá que adaptar estas instruções correspondentemente.

Com a localização da sua chave privada, você pode conectar-se ao cluster, como mostrado a seguir.
```console
ramiro@rbw:~$ ssh -i .ssh/id_ed25519 zumbi.padmec.org
The authenticity of host 'zumbi.padmec.org (150.161.138.10)' can't be established.
ED25519 key fingerprint is SHA256:6KP1lqfcVJrvsp6DC5ucdYXg8awcAMpTcFkw9m8KFDQ.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'zumbi.padmec.org' (ED25519) to the list of known hosts.
Enter passphrase for key '.ssh/id_ed25519': 
Web console: https://zumbi:9090/ or https://192.168.100.10:9090/

Last login: Thu Apr  9 19:12:26 2026 from 150.161.138.218
[ramiro@zumbi ~]$
```

Na primeira vez que você se conecta a um sistema remoto, aparece aquela mensagem meio assustadora, dizendo que a autenticidade do host remoto não pode ser estabelecida. Isto é normal. Digite "yes" para aceitar e continuar a conexão.

O ssh identifica não só o cliente, como também os servidores, através de pares de chaves, para proteger os clientes de ataques via DNS. Se você tivesse instalado a chave pública do Zumbi no seu cliente antes da conexão, esta mensagem não teria aparecido. Nas próximas vezes que você acessar o Zumbi via ssh, não será mais necessário confirmar a autenticidade do sistema remoto. Veja a abaixo o que acontece quando eu tento conectar uma segunda vez, após sair do sistema remoto.

```console
[ramiro@zumbi ~]$ logout
Connection to zumbi.padmec.org closed.
ramiro@rbw:~$ ssh -i .ssh/id_ed25519 zumbi.padmec.org
Enter passphrase for key '.ssh/id_ed25519': 
Web console: https://zumbi:9090/ or https://192.168.100.10:9090/

Last login: Thu Apr  9 19:27:04 2026 from 150.161.138.218
[ramiro@zumbi ~]$
```
Não apareceu a mensagem de confirmação de autenticidade porque a chave pública do Zumbi já foi armazenada localmente, no arquivo `.ssh/known_hosts`. Não brinque com este arquivo. Se tudo correu bem, você tem uma sessão de terminal no nó de login do Zumbi.

Perceba que o cliente de ssh pergunta a sua frase secreta a cada tentativa de conexão. Em princípio, você precisa digitar a sua frase secreta todas as vezes em que tenta se conectar a um sistema remoto. Se a sua frase secreta for decente, isto perde a graça muito rapidamente. A solução **não é** remover a frase secreta ou usar uma frase secreta simples, vamos descrever a solução em instantes.

## ssh-agent

Para não ter que entrar com a sua frase secreta todas as vezes em que você usa o ssh, você pode usar o `ssh-agent`. Este programa roda automaticamente em todos as distribuições do Linux decentemente configuradas. Caso a sua não seja uma delas, tente seguir [estas instruções](https://www.ssh.com/academy/ssh/agent).

Para verificar se você já tem o ssh-agent configurado, tente o comando mostrado abaixo, que deve terminar sem nenhuma mensagem de erro, e sem informar nada de útil também, já que você não adicionou nenhuma chave extra.

```console
ramiro@rbw:~$ ssh-add -l
The agent has no identities.
ramiro@rbw:~$ 
```

O ssh-agent é um programa que fica rodando o tempo todo no background de sua sessão de login gráfica na sua estação de trabalho. Este agente funciona guardando a sua chave secreta decifrada na memória do computador enquanto a sua sessão de login estiver ativa. Você só precisará digitar a sua frase secreta uma vez, antes de usar o ssh pela primeira vez. Depois de carregar sua chave secreta no agente, o cliente de ssh consulta o agente todas as vezes em que necessita de uma chave secreta decifrada, e, se encontrá-la no agente, a usa diretamente. Você interage com o agente usando o programa `ssh-add`.

Vejam o exemplo a seguir. Vamos armazenar a chave secreta no agente.

```console
ramiro@rbw:~$ ssh-add .ssh/id_ed25519
Enter passphrase for .ssh/id_ed25519: 
Identity added: .ssh/id_ed25519 (ramiro@rbw)
ramiro@rbw:~$
```

Percebam que foi necessário entrar com a sua chave secreta. Você pode verificar se o carregamento da chave funcionou,

```console
ramiro@rbw:~$ ssh-add -l
256 SHA256:1QY3jJsUUKREc8igWEv8AJpM6S06rbUOpAZLF8+qG54 ramiro@rbw (ED25519)
ramiro@rbw:~$ 
```

Aparentemente, tudo certo. Para fazer o login no sistema remoto, você agora usa o ssh normalmente,
```console
ramiro@rbw:~$ ssh -i .ssh/id_ed25519 zumbi.padmec.org
Web console: https://zumbi:9090/ or https://192.168.100.10:9090/

Last login: Mon Mar 30 11:32:05 2026 from 150.161.138.218
[ramiro@zumbi ~]$
```

Perceba que não foi solicitada a frase secreta, e nem mesmo uma senha para o sistema remoto, o que é muito conveniente.  Enquanto você permanecer com sua sessão de login ativa na sua estação de trabalho, você pode usar o ssh e os programas associados quantas vezes quiser, sem precisar identificar-se novamente. Na verdade, o cliente de ssh tenta todas as chaves secretas que estão carregadas no agente até encontrar uma que funcione, você nem precisa especificar a chave secreta na linha de comando. Isto é extramente conveniente.

```console
ramiro@rbw:~$ ssh zumbi
Web console: https://zumbi:9090/ or https://192.168.100.10:9090/

Last login: Thu Apr  9 19:27:31 2026 from 150.161.138.218
[ramiro@zumbi ~]$ 
```

## Ecossistema
O ecossistema do ssh tem sido desenvolvido há algumas décadas e é muito poderoso e sofisticado. Permite copiar arquivos do computador local para o remoto,

```console
ramiro@rbw:~$ scp NovelPorousFormulation_TSha.pdf zumbi.padmec.org:
NovelPorousFormulation_TSha.pdf              100% 1372KB  57.4MB/s   00:00
ramiro@rbw:~$
```
e vice-versa,

```console
ramiro@rbw:~$ scp zumbi.padmec.org:Makefile .
Makefile                                    100%   37KB  17.3MB/s   00:00
ramiro@rbw:~$ 
```
e rodar comandos não iterativos no sistema remoto,

```console
ramiro@rbw:~$ ssh zumbi.padmec.org ls -shC
total 1,9M
4,0K benchmarks     4,0K id_ed25519.pub			         4,0K src
108K config.status  368K libtool			             4,0K test
   0 data	         40K Makefile			             4,0K tmp
4,0K doc	        4,0K man				             4,0K Work
4,0K Downloads	    1,4M NovelPorousFormulation_TSha.pdf
4,0K examples	    4,0K run_pvserver.txt
```
e muitas outras coisas muito sofisticadas, como por exemplo, mapear uma porta de conexão internet do sistema local para o sistema remoto e vice-versa, "tunelando" conexões via ssh. Antes de embarcar nesta jornada é conveniente que você entenda onde seus [arquivos ficam armazenados no Zumbi](/storage).

Vale muito a pena ler os manuais dos comandos associados ao OpenSSH, [disponíveis](https://www.openssh.org/manual.html)  em glorioso HTML dos anos 90.

