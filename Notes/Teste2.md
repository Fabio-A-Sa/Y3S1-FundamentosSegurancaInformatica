# Preparação para o Teste 2

## Tópicos

1. Criptografia
2. PKI
3. Autenticação
4. Segurança de Redes
5. Malware e Deteção
6. TLS

## 1 - Criptografia

As criptografias simétrica e assimétrica garantem confidencialidade. A autenticidade/integridade já é garantida por assinaturas digitais, acordos de chaves e MAC. O não-repúdio é só garantido por assinaturas.

### 1 - Cifras simétricas

Há uma chave de longa duração partilhada entre dois utilizadores. Para a mesma chave ser usada várias vezes naquele canal pode ser partilhado um *nouce público*, como por exemplo um número de sequência. A chave é de 128 bits e como a mensagem pode ser muito maior usa-se o OTP com um PseudoRandomGenerator para gerar uma string única para poder cifrar. 
