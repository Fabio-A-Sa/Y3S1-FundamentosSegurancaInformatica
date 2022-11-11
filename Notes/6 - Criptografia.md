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

## Message Authentication Codes MAC

É um algoritmo público e standard. As chaves continuam a ser de 128 bits e existem tags de dimensão pequena (256 bits). As mensagens são públicas. Na recepção da mensagem recalcula-se a tag com a mesma chave e verifica-se se é a mesma. Garante autenticidade e integridade.<br>
MAC não permite detectar se a mensagem e a tag não forem enviadas ou se forem enviadas várias vezes (pode comprometer a ordem dos valores/inputs). É necessário transmitir apenas uma vez e com recurso a número de sequência.

### Construção de MACs

#### HMAC

Construção de um MAC a partir de uma função de Hash. 
Tag = Hash (OKEY, Hash (IKEY, message)), com OKEY = KEY and ConstO, IKEY = KEY and ConstI.

#### Poly1305

Um polinómio é definido por m e avaliado no ponto r. No final adicionar uma constante s. 
Tag = f (m, r) + s

## Confidencialidade e Autenticidade

