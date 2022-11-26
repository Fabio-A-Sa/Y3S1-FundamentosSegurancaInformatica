# 7 - Public-Key Infrastructure (PKI)

É necessário provar a autenticidade do par chave pública e chave privada. Para isso usa-se um canal autenticado TTP (*Trusted-Third-Party*) que reune conhecimento sobre chaves e que pode certificar se determinada chave pertence a determinada entidade. No entanto há problemas de:
- Construção do canal de comunicação com o TTP;
- Estado offline;
- É irrealista, é impossível haver apenas uma TTP para todos;
- Como garantir que as duas entidades em comunicação confiam no mesmo TTP;

## Certificados de Chave pública

Uma pessoa prova a uma autoridade de certificação (AC) que possui uma chave privada, através da assinatura de um pedido de certificado que contém uma chave privada usando a chave secreta ou porque a AC dá a própria chave privada. Depois a AC gera um documento eletrónico com toda a informação recolhida (tanto da origem como da própria máquina), e a validade para limitar a duração da responsabilização do contrato.
Esse documento é codificado com regras ASN1 (*Abstract Syntax Notation*) e DER (*Distinguished Encoding Rules*). Contém identificadores de objecto (OI) e extensões (críticas ou não críticas). 

### Enxtensões de certificados

- Critical, se não conseguir identificar o que significa então devemos rejeitar o certificado;
- Subject/Authority key identifier, com hash da chave pública do CA;
- Basic constraints, flag que assinala certificados como pertencentes ao CA;
- Key usage, a própria CA pode restringir a utilização do certificado;

O titular da chave pública guarda o seu próprio certificado, o que permite enviá-lo até por canais inseguros para o destinatário e assim reduz a dependência da CA (pode inclusive estar offline no processo). O destinatário pode verificar a correção da identidade, a validade e a meta-informação. Mas continua sem saber como verificar se a CA é de confiança nem a obter a chave pública da CA. 

## Public-Key Infrastructure

Constituída por duas grandes bases: infraestruturas físicas de proteção e muita documentação. A documentação deve conter os seguintes aspectos: 

- `Normas técnicas`, que algoritmos e formatos utilizar;
- `Regulamentação`, como deve ser utilizadas as normas técnicas e responsabilidade/direitos dos participantes;
- `Leis`, garantias formais e penalizações em caso de violação das regras;

Há uma confiança implícita nos Certificados CA, pois são normalmente dados pelo próprio sistema operativo (ou seja, por canal seguro). Nada é mais confiável do que o *root CA*, pois é a que gera as chaves de Certificados mais próximos dos utilizadores finais.

### Revogação de certificados

Inicialmente fazia-se através das CRLs (*Certificate Revokation List*) das CAs, que listava os certificados que não eram para ser usados apesar de estarem dentro da validade. Atualmente existem as seguintes soluções:

#### 1 - Trusted Service Provider Lists (TLS):

É uma white list que é atualizada periodicamente contendo os certificados válidos. É usada para comunidades pequenas, fechadas e de segurança elevada.

2. Online Certificate Status Protocol (OCSP):
É um servidor seguro gerido pela própria CA que verifica o estado da revogação usada em contextos de organizações governamentais. Implica 

3. Certificate pinning:
