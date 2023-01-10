# Preparação para o Teste 2

## Tópicos

1. Criptografia
    - 1.1 Cifras simétricas
    - 1.2 Autenticidade
    - 1.3 Cifras assimétricas
2. PKI
3. Autenticação
4. Segurança de Redes
5. Malware e Deteção
6. TLS

## 1 - Criptografia

As criptografias simétrica e assimétrica garantem confidencialidade. A autenticidade/integridade já é garantida por assinaturas digitais, acordos de chaves e MAC. O não-repúdio é só garantido por assinaturas.

### 1.1 - Cifras simétricas

Há uma chave de longa duração partilhada entre dois utilizadores. Para a mesma chave ser usada várias vezes naquele canal pode ser partilhado um *nouce público*, como por exemplo um número de sequência. A chave é de 128 bits e como a mensagem a cifrar pode ser muito maior usa-se o OTP com um PseudoRandomGenerator e Cifras de Bloco iterativamente. Uma cifra de bloco mais usada é o AES (antes era o DES), que não é segura se se usar o mesmo *nouce* (Electronic Codebook ECB, por exemplo). Melhor é usar iterativamente, Cipher Block Chaining (CBC) ou Counter Mode Encryption, este último é fácil de ser usada em hardware.

Por um lado garantem confidencialidade e é formalmente segura. Mas por outro lado a partilha e gerenciamento de chaves é algo não escalável, e tem o problema das mensagens não terem prova de autenticidade (*Man-in-the-Middle*).

### 1.2 - Autenticidade

Garantida por MAC, que é um selo de 256 bits enviado juntamente com a mensagem codificada assim como o número de sequência para garantir não duplicações ou faltas de partes da mensagem integral. Permite identificar se houve manipulação da mensagem pelo caminho. 

### 1.3 - Cifras assimétricas

