---
draft: true
title: 'Secure Shell'
weight: 20
cascade:
    type: docs
---
A criptografia de chave pública foi umas das grandes conquistas intelectuais da humanidade no século 20, vamos torcer para que a computação quântica nunca dê certo e possamos continuar usando-a por muitos e muitos anos.

A ideia geral da criptografia de chave pública é que você pode criar um par de chaves, que são apenas números gigantescos. O par é constituído de uma chave pública e uma chave privada, sempre associadas. Você pode divulgar a sua chave pública para quem você quiser, pode postar no Facebook, Twitter, usar no seu perfil do Instagram, não importa, ela é pública, por definição. Qualquer pessoa de posse da sua chave pública pode criptografar um arquivo que apenas você pode decifrar, usando a sua chave privada. Que, obviamente, deve permanecer conhecida apenas por você, nunca divulgue ou envie de qualquer forma a sua chave privada para ninguém.

Assim, duas pessoas que conheçam as chaves públicas uma da outra, podem trocar mensagens completamente secretas, sem ter compartilhado previamente nenhum segredo, como uma senha comum, que era o único mecanismo disponível para comunicação secreta antes da descoberta da criptografia de chave pública. O impacto disto na computação moderna foi, e continha sendo, tremendo.

No contexto de acesso remoto, o protocolo ssh usa a criptografia de chave pública para que os computadores envolvidos negociem entre si uma chave de criptografia normal  que é usada para criptografar a sessão.
Nada disto é muito importante para nós, e eu estou longe de entender este assunto, então partir diretamente para a criação de um par de chaves. Se você já usar o ssh e já tem um par de chaves que deseja usar, não há problema, pode usá-la para acessar o Zumbi, mas por favor antes de enviar a sua chave pública para nós certifique-se que ela esteja armazenada em um formato compatível com o [OpenSSH](https://www.openssh.org/).
Caso você ainda não tenha um par de chaves, podemos ajudar a [criá-lo](/ssh/keys).

