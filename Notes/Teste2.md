# Preparação para o Teste 2

## Tópicos

1. Criptografia
    - 1.1 Cifras simétricas
    - 1.2 Autenticidade
    - 1.3 Aleatoriedade
    - 1.4 Cifras assimétricas
    - 1.5 Assinaturas digitais
    - 1.6 Acordos de chaves
2. PKI
    - 2.1 Certificados e CA
    - 2.2 Revogação de Certificados
3. Autenticação
    - 3.1 Autenticação Multifactor
4. Segurança de Redes
    - 4.1 - Ataques à camada física e lógica
    - 4.2 - Ataques à camada de rede (IP)
    - 4.3 - Ataques à camada de transporte
5. Malware e Deteção
6. TLS

## 1 - Criptografia

As criptografias simétrica e assimétrica garantem confidencialidade. A autenticidade/integridade já é garantida por assinaturas digitais, acordos de chaves e MAC. O não-repúdio é só garantido por assinaturas.

### 1.1 - Cifras simétricas

Há uma chave de longa duração partilhada entre dois utilizadores. Para a mesma chave ser usada várias vezes naquele canal pode ser partilhado um *nouce público*, como por exemplo um número de sequência. A chave é de 128 bits e como a mensagem a cifrar pode ser muito maior usa-se o OTP com um PseudoRandomGenerator e Cifras de Bloco iterativamente. Uma cifra de bloco mais usada é o AES (antes era o DES), que não é segura se se usar o mesmo *nouce* (Electronic Codebook ECB, por exemplo). Melhor é usar iterativamente, Cipher Block Chaining (CBC) ou Counter Mode Encryption, este último é fácil de ser usada em hardware.

Por um lado garantem confidencialidade e é formalmente segura. Mas por outro lado a partilha e gerenciamento de chaves (mesmo com o KDC - Key Distribution Center) é algo não escalável, e tem o problema das mensagens não terem prova de autenticidade (*Man-in-the-Middle attack*).

### 1.2 - Autenticidade

Garantida por MAC, que é um selo de 256 bits enviado juntamente com a mensagem codificada assim como o número de sequência para garantir não duplicações ou faltas de partes da mensagem integral. Permite identificar se houve manipulação da mensagem pelo caminho. Para garantir confidencialidade e autenticidade é necessário usar o método `Encrypt then Mac`, só se começa a decifrar quando o MAC corresponde. Se considerarmos a segurança dos metadados usamos o AEAD (*Authenticated Encryption with Associated Data*) com AES-GCM.

### 1.3 - Aleatoriedade

Usando fontes de entropia, o computador permite aleatoriedade em /dev/urandom e /dev/random. O primeiro é non-blocking e o segundo bloqueia até atingir um patamar satisfatório de entropia. O melhor é o non-blocking pois o outro devido à medição (como medir o grau de entropia?) acaba por bloquear e retornar nulo, que é um valor constante e nada aleatório.

### 1.4 - Cifras assimétricas

Usadas porque não é possível garantir o não-repúdio nem gerir chaves simétricas de longa duração em ambientes síncronos (onde é usada acordos de chave e assinaturas digitais) e assíncronos (onde são usadas estas chaves). Devido à complexidade computacional chaves de milhares de bits são usadas para cifrar uma chave de 128 bits que cifra, de forma simétrica, o payload a transmitir. Pode haver vários transmissores mas só um receptor (o que tem a chave privada), usando um *trapdoor permutation* como o RSA. A garantia que a chave pública pertence àquela entidade é dada por PKI.

### 1.5 - Assinaturas digitais

Não são repudiáveis (intrínsecas a uma só pessoa), ao contrário dos MACs. A ideia é assinar o documento original e só depois cifrar, para não poder alegar que desconhecia o conteúdo do envelope digital. Os envelopes digitais têm de ter um destinatário.

### 1.6 - Acordos de chaves

Comprometer chaves de longa duração não compromete chaves de sessões passadas. Usa-se através do protocolo Diffie-Hellman autenticado, que garante proteção contra ataques *Man-in-the Middle*.

## 2 - Public Key Infrastructure

Necessária para provar a autenticidade do par de chaves públicas e privadas. Usar um TTP (*Trusted-Third-Party*) está fora de questão pois é impossível haver TTP para todos ou ambos terem ligações à mesma TTP. Usa-se então um CA (*Certificate Authority*), que emite certificados que garantem a autenticidade das chaves públicas e privadas de determinadas entidades. 

### 2.1 - Certificados e CA

Estes certificados podem depois ser enviados em canais abertos. Como validar um certificado? Através da chave pública da própria CA, que é conhecida através de conhecimento anterior de CA ou na cadeia de certificados que já vêm nos sistemas operativos (têm de vir de canais seguros sempre). Nada é mais confiável do que o root CA, pois é a que gera as chaves de Certificados mais próximos dos utilizadores finais.

### 2.2 - Revogação de Certificados

A revogação de certificados pode ser feita através de *certificate revokation list*. Existem dois protocolos:
- TSL: Trusted Service Provider Lists, para comunidades pequenas;
- OCSP: Online Certificate Status Protocol, por motivos organizacionais;
- Certificate Pinning: Empresas contém as suas próprias white lists que são atualizados periodicamente;

## 3 - Autenticação

Gera-se aleatoriamente no momento um componente (timestamp ou outro) de tempo de vida muito curto e envia-se à entidade que se quer autenticar. A entidade terá de cifrar o componente e enviar, provando que está ativo naquele momento. As provas de identidade podem ser com passwords, algo que se possui (telemóveis e smartcards), ou por biometria. O armazenamento das passwords deverá ser feito criptograficamente com *salt*, que não é secreto, e cifras lentas, para inibir ataques de dicionário. 

### 3.1 - Autenticação Multifactor

- `Smartcards`, não têm interface com o utilizador mas tem um microprocessador capaz de garantir defesa em profundidade;
- `One-Time Token`, tem um segredo pré-partilhado com o servidor, e pede a password e o MAC gerado na altura. Pode existir ataque man-in-the middle e a phishing e o servidor tem de guardar chaves secretas;
- `One-Time Passcode`, envia tokens para o telemóvel, custa menos um dispositivo mas há menos independência entre factores;

## 4 - Segurança de Redes

Os protocolos de redes não têm qualquer segurança ao nível de usurpação de pacotes. Podem haver diversos tipos de ataques (eavesdropper, off path, in path e man-in-the-middle):

### 4.1 - Ataques à camada física e lógica

- `Wiretappinig`: escuta num canal de comunicações;
- `Eavesdropping/Packet sniffing`: recolher e armazenar pacotes trocados entre entidades legítimas;
- `MAC flooding`: Injeção de imensas mensagens com novos MACs, a tabela dos switches fica cheia e faz broadcast;
- `MAC spoofing`: Usurpar MAC address, criar uma máquina com o mesmo MAC para também poder receber os pacotes;

### 4.2 - Ataques à camada de rede (IP)

- `ARP poisoning/spoofing`: convence a outra máquina que eu tenho aquele IP, permite Man-in-the-middle;
- `Hijacking de routing`: convencer a rede que somos um router;
- `Rogue DHCP`: o protocolo DHCP dá IPs a clientes que o pedem ao servidor. Usurpar o funcionamento disto é o mais típico ataque;
- `DNS spoofing`: máquina maliciosa a fazer de servidor DNS;
- `DNS (cache) poisoning / Kaminsky Attack`: sem usurpar o DNS, bombardear o DNS legítimo com informação falsa;

### 4.3 - Ataques à camada de transporte

- `Terminação de Ligações`: através da firewall pode haver término de ligações; 
- `Spoofing às cegas, off path`:  estabelecer uma sessão em nome de uma origem que não controlamos, baseado em adivinhar os números de sequências;
- `TCP Session Hijacking`: depois da autenticação da sessão, aproveitamos o estado da sessão;
- `UDP Hijacking`: não há controlo de tráfego, logo pode-se interferir na transferência de pacotes e tenta responder primeiro aos endpoints;

As `Firewalls` funcionam como uma barreira que filtra os cabeçalhos dos pacotes que passam para a rede interna, usando uma política de controlo de acessos. A filtragem pode ser sem estado ou com estado, sendo o último com melhor performence pois regista o contexto e analisa o pacote naquele contexto, é mais permissiva.

## 5 - Malware e Detecção

