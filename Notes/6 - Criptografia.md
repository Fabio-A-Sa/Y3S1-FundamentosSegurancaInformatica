# 6 - Criptografia

Área multidisciplinar com princípios rigorosos e que obedece a normas internacionais, para proteção da confidencialidade, autenticidade/integridade e não-repúdio. Está presente em todas as aplicações quotidianas através da utilização de funções **one way**. A proteção da segurança da informação pode ser:
- Em trânsito: síncrona, em tempo real como o HTTPs, ou assíncrona, como o email, wtp;
- Em repouso: criptografia de discos, cloud;

## Cifras simétricas

Mecanismo público que tem dois algoritmos: E (encrypt, K, N, M) e D (decrypt, K, N, C). Existe uma chave secreta normalmente de 128 bits / 16 bytes, que é aleatória e só os vértices da comunicação é que conhecem. As chaves são de 128 bits pois já dá garantias de segurança (2^128 possibilidades) e é mais económico. Também existe um n, nouce, que é um parâmetro público que permite que exista reutilização da mesma chave. <br>
Garante a confidencialidade mas não garante integridade/autenticidade em ataques ativos, pois o atacante pode mudar os bits da chave (*bit flip*) e usurpar a mensagem original transmitida.

### Casos de uso

Uma chave pode ser usada apenas uma vez (*one-time keys*). Como o email, que usa uma chave para cada mensagem. Aqui o nouce não é relevante, pois pode ser fixado a 0.

Uma chave pode ser usada muitas vezes (*many-time key*). Como a cifra de disco, HTTP e TLS. Aqui o nouce não se pode repetir, é gerado com número aleatório ou número de sequência.

### One Time Pad

Inventado por Vernam em 1917. Cifra-se usando um XOR entre a mensagem com uma chave k de bits que não se volta a repetir. Para descriptografar, usa-se uma propriedade do XOR: c XOR k.

#### Vantagens:

- Garante confidencialidade;
- É formalmente segura, a distribuição do criprograma é totalmente aleatória;

#### Desvantagens:

- A chave k é do tamanho do texto a cifrar, e como não pode ser repetida acaba por ser computacionalmente difícil transmitir e/ou criar;
- A chave é usada apenas uma vez;

#### Gerador Pseudo-Aleatório (PRG)

Através de uma *seed* de 128 bits gera uma chave K do tamanho do texto a cifrar. No entanto isto não é seguro pois é determinístico: ao usar a mesma seed, gera a mesma chave. Solução: nos PRGs modernos, além da seed colocar o valor do nouce público.

### Cifras de Bloco

Permitem construir cifras. Dado uma chave aleatória K e um input B de 16 bytes, calcula-se o output. Os outputs do algoritmo não se conseguem distinguir de outputs aleatórios. Como a cifragem é feita por iterações de blocos quadrados de 4 por 4 bytes, podem ser implementadas em hardware, o que as torna mais eficientes. Exemplos destes algoritmos:
- `DES`, Data Encryption Standard, usado até 2000 com blocos de 64 bits e chaves até 56 bits;
- `AES`, Advanced Encryption Standard, usado desde 2000 com blocos de 128 bits e chaves de 128 até 512 bits;

#### Eletronic Code Book (ECB)

Método inseguro de utilização das cifras de bloco, pois blocos do ficheiro iguais geral blocos do criptograma igmais.

#### Cipher Block Chaining (CBC)

Mais seguro, porém complexo para implementar em hardware, logo é menos eficiente. Para a cifragem de cada bloco existe um parâmetro de input adicional que é público e corresponde a:
- um initialization vector (IV), se o bloco for o primeiro;
- O output da cifragem do bloco anterior, caso contrário;

#### Counter (CTR)

A cifra de blocos recebe a chave K e um input constituido metade por um nouce e outra por um contador iniciado em zero. No final a cifragem é garantida fazendo um XOR entre a mensagem a encriptar e o output da cifra de blocos. É uma aproximação ao One Time Pad.

#### ChaCha20

Mais eficiente do que o AES em implementações de software. É uma cifra sequencial com nonce e com um gerador pseudo-aleatório dedicado.

## Message Authentication Codes MAC

É um algoritmo público e standard. As chaves continuam a ser de 128 bits e existem tags de dimensão pequena (256 bits). As mensagens são públicas. Na recepção da mensagem recalcula-se a tag com a mesma chave e verifica-se se é a mesma. Garante autenticidade e integridade.<br>
MAC não permite detectar se a mensagem e a tag não forem enviadas ou se forem enviadas várias vezes (pode comprometer a ordem dos valores/inputs). É necessário transmitir apenas uma vez e com recurso a número de sequência.

### Construção de MACs

#### HMAC

Construção de um MAC a partir de uma função de Hash. 
Tag = Hash (OKEY, Hash (IKEY, message)), com OKEY = KEY XOR ConstO, IKEY = KEY XOR ConstI.

#### Poly1305

Um polinómio é definido por m e avaliado no ponto r. No final adicionar uma constante s. 
Tag = f (m, r) + s

## Confidencialidade e Autenticidade

Existem três hipóteses principais para combinar a confidencialidade e a autenticidade:

- Encrypt and Mac, usado no SSH, calcula o criptograma e o MAC sobre a mesma mensagem e envia em separado;
- Mac then Encrypt, usado no SSL, calcula o criptograma de (mensagem + MAC da mensagem);
- Encrypt then Mac, usado no IPSEC, calcula o criptograma da mensagem e calcula o MAC do criptograma e envia os dois em separado;

A melhor solução é a usada no IPSEC, pois só começa a decifrar quando o MAC corresponde. No caso do SSL haveria dados possivelmente manipulados e maliciosos em memória antes da verificação.

## Authenticated Encryption with Associated Data (AEAD)

Abstração da implementação de um canal seguro com criptograma simétrico. Garante a confidencialidade da mensagem, a autenticidade do criptograma e a autenticidade dos metadados. O algoritmo mais usado no mundo é o AES-GCM (Galois-Counter-Mode).

## Aleatoriedade

Atingir a aleatoriedade é difícil. Por isso os mecanismos dos sistemas operativos usam um hash das suas fontes de entropia (fenómenos físicos como medição de temperatura, atividade do utilizador, strings, floats, bautrate). Sempre que há pedido de aleatoriedade nova, existe uma atualização do estado da *entropy pool* e há dois processos em Linux nesse sentido:

- `/dev/urandom`, non-blocking, fornece dados simples;
- `/dev/random`, blocking, só fornece dados aleatórios se atingir um patamar seguro de entropia;

O mais seguro é o non-blocking. O blocking é menos recomendado porque por um lado não há garantias da correcta medição da entropia por parte do sistema e por outro, como nem sempre tem dados para enviar, acaba por retornar algumas vezes nulo, que não é um dado aleatório.

## Gestão de Chaves

Com criptografia simétrica, para N agentes de comunicação é necessário partilhar na ordem de N^2 chaves, o que provoca uma distribuição manual de chaves pouco prática e dificuldades a adicionar um novo agente à rede.

Antigamente, para sistemas fechados, utilizava-se um KDC (*Key Distribution Center*) que armazena uma chave de longa duração partilhada por cada agente para aceder à rede. Assim é só necessário N chaves para N agentes. No entanto o KDC está sempre online, o que constitui um ponto central de falha.

### Limitações com a criptografia simétrica

Sempre que é possível, usar criptografia simétrica, pois poupa recursos (economia de mecanismos). Por outro lado, não é possível existir chaves simétricas de longa duração. 

## Criptografia assimétrica

## Cifras de chave pública 

Para comunicações unidirecionais. Existem duas chaves (uma pública, que serve para cifrar, e uma privada, para decifrar). Os algoritmos usados são públicos.

São muito mais ineficientes do que as cifras simétricas, pois podem ter milhares de bits ao contrário dos anteriores 128. Para ser mais eficiente o emissor cifra:

- C1 = ENC(pk, ks), ks = chave de sessão, pk = chave pública do receptor
- C2 = AEAD(ks, M), M = mensagem a encriptar
- O receptor recupera KS a partir de C1 e da sua chave privada, e recupera M a partir de KS. 

### Construção de cifras de chave pública 

Através do mecanismo de OAEP. Existe uma função F que codifica M com uma chave pública mas o inverso só é possível com input e chave privada. O input tem de ser cifrado antes (ou seja, aleatoriedade), pois caso contrário pode haver comparações de input e descobrir mesmo sem a chave privada a mensagem.
A função de chave pública (F) é RSA atualmente. Usa-se dois números primos grandes e secretos e um expoente comum, 0x10001. 

## Assinaturas Digitais

Uma entidade usa uma `chave de assinatura` (chave secreta) para codificar a mensagem e envia a cifra com a tag. Qualquer outra entidade pode decifrar, usando uma `chave de verificação` (chave pública) e a tag resultante. Consegue provar a autenticidade das mensagens trocadas. Isto assume que a chave de verificação veio da entidade que estamos a tentar verificar. 

## Envelopes digitais

Permite combinar cifras assimétricas com assinaturas digitais, garantindo a autenticidade, integridade e não repúdio. A mensagem é assinada e só depois cifrada, para ninguém alegar que assinou um criptograma sem conhecer o seu conteúdo.<br>
Para garantir o não repúdio, é necessário também que na mensagem exista indicação de quem é o destinatário.

## Acordo de Chaves

Garantem o *perfect forward secrecy*, ou seja, comprometer chaves de longa duração não compromete chaves de sessões passadas. 

### Protocolo Diffie-Hellman

Utilização de curvas elíticas pois é mais eficiente. Cada elemento da comunicação calcula um valor aleatório x e manda G^x. Do outro lado, a chave resultante que vai ser usada na troca de mensagens é (G^x)^y. <br>
Este protocolo está vulnerável a *Man in the Middle Attack*: não há qualquer identificação dos utilizadores em comunicação, pelo que é possível trocar a origem. 

### Protocolo Diffie-Hellman Autenticado

Semelhante ao anterior, mas cada G^x e G^y estão autenticados. Durante a criação das chaves há verificação da autenticidade da mensagem através da chave pública de ambos.