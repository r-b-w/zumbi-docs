---
draft: false
title: 'Criação de Chaves'
weight: 20
cascade:
    type: docs
---

O procedimento para criar seu par de chaves depende do programa que você usa. Depende também, indiretamente, do  sistema operacional que você usa, no sentido de que os programas tipicamente usados em cada sistema operacional são diferentes.

Na conexão via secure shell, você executa um programa no seu computador local, o cliente ssh, que pode ter incontáveis nomes diferentes, e coneta-se a um computador remoto onde é executado um outro programa, o servidor de ssh, que é responsável por autenticar o cliente e manter a conexão ativa.

A geração do par de chaves é feita no seu computador pessoal, usando ou o cliente de ssh, ou algum outro programa específico para isto. Em geral, estes programas perguntam se você deseja proteger sua chave privada com uma "passphrase". Uma passphrase é uma frase de segurança, que funciona como uma senha para o sua chave privada. Antes de usá-la para qualquer coisa, você precisa decifrá-la com a chave de segurança.

A sua frase de segurança deve ser tratada como um segredo, portanto deve ser conhecida apenas por você e nunca informada a ninguém. No contexto do ssh, normalmente usa-se uma frase sem sentido, construída com umas quatro ou cinco palavras do dicionário, que você consiga se lembrar com uma certa facilidade. Por exemplo "meu cachorro usa camisa preta quando dirige". Os programas tipicamente oferecem a possibilidade de gerar o par de chaves sem senhas. Isto é uma ideia **muito ruim**, **não faça isto**.

É muito comum que uma pessoa use o mesmo par de chaves para acessar um grande número de computadores diferentes. Eu mesmo devo ter gerado apenas uns 4 ou 5 pares em uns 20 anos. Neste caso, se a sua chave privada, desprotegida por uma frase secreta, for comprometida, o acesso a **todos** os computadores nos quais você usou a sua chave pública foi comprometido também. Se a sua chave privada é protegida por uma frase secreta e for comprometida, nada acontece, desde que você tenha usado uma frase secreta não completamente tola.

Temos guias ultra resumidos para geração do par de chaves no [Linux](/ssh/keys/linux) e no [Windows](/ssh/keys/windows). Fique à vontade para ignorá-los se você souber o que está fazendo.
