---
draft: false
title: 'Acesso'
weight: 10
cascade:
    type: docs
---

O Zumbi só pode ser acessado remotamente, via internet. Caso você deseje acessá-lo de fora da rede interna da UFPE, será necessário que você ative o [serviço de VPN da UFPE](https://sites.ufpe.br/cstic/catalogo-servicos/acesso-remoto-vpn/). Se você já possui algum esquema alternativo para acessar seu próprio computador, estando fora da rede da UFPE, você provavelmente não precisa do VPN da UFPE, mas talvez você tenha que usar o seu próprio computador como "ponte" para acessar o Zumbi. As possibilidades são infinitas e não temos como documentar todas elas.

Por segurança, a conexão do seu computador ao Zumbi é feita com o protocolo [Secure Shell](https://www.ssh.com/academy/ssh), conhecido popularmente com ssh. Se você nunca usou este protocolo, você encontrará algumas novidades um pouco exotéricas, mas vamos tentar ajudá-lo a colocar isto para funcionar. Conceitualmente, o acesso ao Zumbi é feito como mostrado abaixo.

{{< figure
src="internal_connection.svg"
alt="Acesso via rede interna da UFPE"
caption="Acesso via rede interna da UFPE"
width="85%"
>}}

{{< figure
src="vpn_connection.svg"
alt="Acesso via VPN"
caption="Acesso via VPN"
width="85%"
>}}

É importante notar que, para todos os efeitos, após estabelecida a sua conexão virtual via VPN da UFPE, seu computador age como se estivesse conectado diretamente à rede interna da UFPE.

Após certificar-se que você consegue acessar a rede interna da UFPE, para abrir uma conta no Zumbi e acessar o sistema, você vai precisar nos enviar uma chave pública ssh. Temos um pequeno [guia](/ssh) para ajudá-lo a criar uma caso você já não tenha alguma  que queira usar.

Depois de enviar a sua chave pública para nós e ter sua conta aberta, você pode acessar o zumbi remotamente via ssh, usando um console de texto ou uma interface gráfica. Antes de fazer isto, no entanto, seria bom você ter uma ideia da arquitetura geral do sistema.

Para acessar o sistema, você se conecta ao endereço zumbi.padmec.org. Este é o endereço do nó de login, ou nó mestre do sistema. Este computador server para gerenciar o acesso ao sistema, armazenar arquivos, submeter jobs ao sistema, mas **nunca**, de forma alguma, deve ser usado para executar qualquer tipo de simulação. Nem por um instante, nem para testar só uma coisinha. Em princípio, até compilações e instalações de programas que levem muito tempo devem ser feitas a partir dos nós computacionais. Isto é uma regra que deve ser e é levada muito a sério. Se percebermos que está sendo violada propositalmente, sua chance de perder acesso ao sistema é enorme.

{{< figure
src="esquema.svg"
alt="Visão esquemática"
caption="Visão esquemática"
width="90%"
>}}

Todas as suas simulações e todo o trabalho que demande recursos computacionais mensuráveis deve ocorrer em um dos nós computacionais, cujos nomes não n01, n02 e n03. Você praticamente nunca vai acessar estes nós usando o ssh diretamente, o sistema, através do gerenciador de jobs, vai alocar automaticamente processadores para você em nos nós nos quais haja disponibilidade e fazer com que seus processos rodem neles. Todos os computadores do sistema podem acessar o mesmo [pool de armazenamento](/storage) de forma transparente, não há necessidade de transferir arquivos de um lugar para o outro dentro do sistema.

Temos também um pequeno [guia de conexão](/ssh) que pode ajudá-lo a acessar o sistema caso você não tenha experiência em usar computadores Linux remotamente.
