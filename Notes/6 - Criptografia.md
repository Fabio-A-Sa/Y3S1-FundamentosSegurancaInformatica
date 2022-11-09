# 6 - Criptografia

Área multidisciplinar com princípios rigorosos e que obedece a normas internacionais, para proteção da confidencialidade, autenticidade/integridade e não-repúdio. Está presente em todas as aplicações quotidianas através da utilização de funções **one way**. A proteção da segurança da informação pode ser:
- Em trânsito: síncrona, em tempo real como o HTTPs, ou assíncrona, como o email, wtp;
- Em repouso: criptografia de discos, cloud;

## Cifras simétricas

Mecanismo público que tem dois algoritmos: E (encrypt, K, N, M) e D (decrypt, K, N, C). Existe uma chave secreta normalmente de 128 bits / 16 bytes, que é aleatória e só os vértices da comunicação é que conhecem. As chaves são de 128 bits pois já dá garantias de segurança (2^128 possibilidades) e é mais económico. Também existe um n, nouce, que é um parâmetro público que permite que exista reutilização da mesma chave.

### Casos de uso

Uma chave pode ser usada apenas uma vez (*one-time keys*). Como o email, que usa uma chave para cada mensagem. Aqui o nouce não é relevante, pois pode ser fixado a 0.

Uma chave pode ser usada muitas vezes (*many-time key*). Como a cifra de disco, HTTP e TLS. Aqui o nouce não se pode repetir, é gerado com número aleatório ou número de sequência.

### One Time Pad

Inventado por Vernam em 1917. Cifra-se usando um XOR entre a mensagem com uma chave k de bits que não se volta a repetir. Para descriptografar, usa-se uma propriedade do XOR: c XOR k = m XOR k XOR k.

#### Vantagens:

- Garante confidencialidade;
- É formalmente segura, a distribuição do criprograma é totalmente aleatória;

#### Desvantagens:

- A chave k é do tamanho do texto a cifrar;
- A chave é usada apenas uma vez;

## Cifras assimétricas

