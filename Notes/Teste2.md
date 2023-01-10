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
    - 
3. Autenticação
4. Segurança de Redes
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

