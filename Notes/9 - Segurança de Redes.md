# 9 - Segurança de Redes

## Rede

A rede de internet concentra a complexidade nos pontos terminais e surge do encaminhamento de pacotes de dados por ligações físicas. A comunicação é feita com base em protocolos, que especificam a sintaxe, semântica e normas segundo o modelo de camadas OSI. Existem cinco camadas principais:

- Física
- Data link
- Network
- Transport
- Application

## Protocolos

Usado para uma comunicação sem ligação e poder haver perda de pacotes. É necessário conhecer os endereços IP e MAC dos routers responsáveis por encaminhar os frames, pelo que é necessário existir um protocolo ARP (*Address Resolution Protocol*) e o *Handshake* dos dois lados através do protocolo TCP e ACK responses.<br>
Para traduzir o nome do site para um IP é necessário usar um DNS (*Domain Name Service*).

## Segurança

A estrutura em camadas da rede permite vários tipos de ataques. Tanto a nível físico, como em *endpoints* e infraestruturas. É por isso importante seguir o modelo CIA (confidencialidade, integridade e disponibilidade).

### Ataques à camada física/lógica

#### Wiretappinig

Quando à escuta de um canal de comunicações. No wiretappinig ativo, o atacante além de escutar pode injectar, modificar pacotes ou fazer DoS. É usado em muitos países por serviços de segurança.

### Eavesdropping/Packet sniffing

Recolher e armazenar pacotes trocados entre entidades legítimas. Permite observar todas as comunicaçõe não cifradas, extrair passwords por exemplo. 

### MAC flooding: melhor sniffing

Injeção de imensas mensagens com novos MACs, de modo a que a tabela dos switches fique cheia e eles sejam obrigados a fazer broadcast.

### MAC spoofing: usurpar MAC address

Sempre que sabemos um endereço MAC de uma máquina, podemos usurpá-lo: configurar uma placa de rede para que se pareça com a máquina alvo. Como não há qualquer autenticação na camada lógica, a placa de rede passa a poder receber e enviar pacotes em nomes de terceiros.

### Ataques à camada de rede (IP)

#### ARP poisoning/spoofing

Como os dispositivos fazem cache às respostas do protocolo ARP, acabam por poder ser influenciadas caso um IP seja manipulado. Isto permite fazer ataques Man in the Middle. 

#### Hijacking de routing

O protocolo ICMP permite descobrir um router na rede. Se o atacante se auto-afirmar como router, pode redirecionar o tráfego e inspecionar o conteúdo dos pacotes não cifrados. 

#### Rogue DHCP

O protocolo DHCP permite dar IPs a clientes que o pedem ao servidor. Este ataque consiste em convencer o cliente que o router que deseja está num determinado IP, este por sua vez controlado pelo adversário. É mais um típico ataque Man in the Middle.

#### DNS spoofing

Fornecimento de IPs falsos para nomes de sites fidedignos. 

#### DNS (cache) poisoning / Kaminsky Attack

Bombardear o servidor DNS local com respostas de resoluções DNS, assim o servidor acaba por aceitar respostas que contém um IP controlado pelo atacante, dando depois a entidades legítimas esse IP usurpado.

### Ataques à camada de Transporte

#### Terminação de ligações

Uma firewall pode terminar ligações, enviando mensagens de *reset* para ambas as partes terminais. Usada na *Great Firewall* da China, por exemplo.

#### Spoofing às cegas (off path)

Estabelecimento de uma sessão em nome de uma origem não controlada. Envia-se uma mensagem inicial (SYN) e adivinhamos o número de sequência para poder continuar a comunicar. Uma mitigação possível é usar números de sequência aleatórios.

#### TCP Session Hijacking

Após a vítima criar uma sessão legítima (onde são passados segredos que o atacante inicialmente não sabe), usurpar essa ligação. Há três fases: tracking, des-sincronização (avançar no número de sequência, receber NACKS ou perda de pacotes) e injeção. Não há maneira de contornar a situação a menos de criptografia. 

#### UDP Hijacking

Como não há controlo de tráfego, o atacante pode interferir na transferência de pacotes e tenta responder primeiro que os *endpoints*.

## Defesas

As seguranças da rede são colocadas após a implementação dos protocolos, que muitas vezes não são cifrados.

Define-se uma fronteira entre a organização a proteger e a internet externa. Isto permite proteger a rede interna de ataques externos e de ataques internos por máquinas infetadas. Limita-se o tráfego da rede através de mecanismos:
- Firewalls
- NATs
- Proxies de aplicações especificas
- Network Intrusion Detection Systems

### Firewalls

Existem firewalls locais (nas máquinas do sistema) e firewalls da rede (que interceptam comunicações de/para muitas máquinas e definem a fronteira com o exterior). Filtram os pacotes olhando para os cabeçalhos, percebendo quais são os endereços IP e os protocolos alocados. São diferentes das **proxies**, já que estas últimas só trabalham ao nível de uma aplicação específica.

### Políticas de Controlo de Acessos

As firewalls permitem todos os acessos de dentro para fora mas não de fora para dentro. Para esse caso usa uma *white list* e nega todos os outros acessos a serviços (**default deny**), uma política mais conservadora, provocando sempre alguma instabilidade inicial.

### Filtragem de pacotes

Algumas regras são usadas para receber ou não determinados pacotes:
- Bloquear DNS para servidores não conhecidos;
- Bloquear HTTPS de ligações para IPs fora da organização;
- Bloquear endereços internos desconhecidos;

A filtragem pode ser sem estado ou com estado, quando há registo de um contexto e o pacote terá de pertencer ao contexto, caso contrário é negado. A filtragem pode ser contornada usando portas usadas por serviços legítimos ou encapsular protocolos dentro de outros protocolos legítimos. 

### Network Address Translation

Esta tabela pode ser usurpada para permitir ligações externas a determinados máquinas da rede interna. A tabela NAT permite reduzir exposições ao exterior, não dando diretamente os IPs das máquinas internas da rede, mas por outro lado é fácil fazer *bypass* para adversários ativos. 

### Proxies

Obriga o tráfego de uma ou mais aplicações a passar poe ela, garantindo um controlo de pacotes para averiguar se há algum perigo. É um Man in the Middle "do bem". Usado em servidores STMP, SSH e HTTPS.

