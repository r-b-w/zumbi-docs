---
draft: false
title: 'Windows'
weight: 20
cascade:
    type: docs
---

Há uma infinidade de clientes nativos de ssh para o Windows. O [cliente ssh bitvise](https://bitvise.com/ssh-client) foi escolhido para demonstração aqui não por ser necessariamente melhor do que qualquer outro, mas  apenas por conhecê-lo há muito, muito tempo. Possivelmente você conseguirá adaptar o procedimento aqui descrito para outros clientes ssh.

Em primeiro lugar você precisa [descarregar o programa](https://dl.bitvise.com/BvSshClient-Inst.exe) e instalá-lo. É uma instalação de Windows normal, clique no executável, dê permissão para modificar o sistema, aceite o contrato de licença e aceite os defaults caso você não saiba o que está fazendo. Eu, particularmente, não gosto de icons no meu desktop e não preciso que o programa inicia automaticamente ao final da instalação, então eu desativo estas duas opções, mas isto é a gosto do cliente.

{{< figure
src="install_options.png"
alt="Opções de instalação"
caption="Opções de instalação"
width="60%"
>}}

Caso você tenha desmarcado "Run BitVise SSH Client when done" na etapa anterior, procure o programa na busca de programas do Windows e dê partida nele. Deve aparecer a janela abaixo. Selecione "Client key manager", que está marcado nesta figura.

{{< figure
src="start_window.png"
alt="Janela inicial"
caption="Janela inicial"
width="80%"
>}}

Deve aparecer a janela a seguir, onde você não deve ter nenhum par listado. Vamos gerar um par clicando no botão "Generate New", como indicado.

{{< figure
src="generate_new.png"
alt="Gerenciador de chaves"
caption="Gerenciador de chaves"
width="80%"
>}}

Aparece então a janela na qual você escolhe parâmetros para o seu par de chaves. Não é estritamente necessário, mas, por uniformidade e para facilitar a minha vida, vamos mudar o algoritmo padrão, RSA, para ED25519, selecionando a opção no dropbox, como mostrado a seguir.

Antes de gerar as chaves **certifique-se** que você escolheu uma frase secreta, conforme discutido [anteriormente](/ssh/keys). 

{{< figure
src="key_parameters.png"
alt="Parâmetros do par de chaves"
caption="Parâmetros do par de chaves"
width="60%"
>}}

Então, **após** ter entrado com a sua frase secreta, duas vezes, clique em "Generate".

{{< figure
src="generate_keys.png"
alt="Geração do par de chaves"
caption="Geração do par de chaves"
width="60%"
>}}

Após um curto intervalo deve aparecer uma janela como a mostrada a seguir, com os números e códigos e praticamente tudo *completamente diferente*. As chaves são aleatórias, e se a minha janela aparecesse igual à sua, alguma coisa estaria muito errada.  O par de chaves está pronto para uso por este cliente, mas você precisa exportar a chave pública para que ela seja instalada no Zumbi.

{{< figure
src="generate_result.png"
alt="Resultado da geração"
caption="Resultado da geração"
width="80%"
>}}

Exportamos a chave pública clicando em "Export", o que fará surgir a janela abaixo. Escolha "OpenSSH format". **Não** exporte a chave privada.

{{< figure
src="export_key.png"
alt="Exportação da chave pública"
caption="Exportação da chave pública"
width="70%"
>}}

Vai aparecer então uma janela na qual você escolher o arquivo de destino no qual você quer salvar a sua chave pública. Escolha um nome que você lembre, e um local onde você encontre este arquivo posteriormente, e clique em "Salvar". Este é o arquivo que você deve enviar junto com seu pedido de abertura de contas no Zumbi.

{{< figure
src="save_public_key.png"
alt="Arquivo de destino"
caption="Arquivo de destino"
width="80%"
>}}

Pronto, você pode fechar todas as janelas do cliente BitVise, você só vai precisar dele novamente quando for acessar o sistema via terminal, ou para transferir arquivos. Ou muitas outras coisas.
Depois vamos configurar este cliente para que o seu acesso seja relativamente simples.
Você pode tentar agora [estabelecer uma conexão](/ssh/connection/windows).
