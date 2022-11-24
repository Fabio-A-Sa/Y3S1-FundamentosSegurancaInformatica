# 7 - Public-Key Infrastructure (PKI)

É necessário provar a autenticidade do par chave pública e chave privada. Para isso usa-se um canal autenticado TTP (*Trusted-Third-Party*) que reune conhecimento sobre chaves e que pode certificar se determinada chave pertence a determinada entidade. No entanto há problemas de:
- Construção do canal de comunicação com o TTP;
- Estado offline;
- É irrealista, é impossível haver apenas uma TTP para todos;
- Como garantir que as duas entidades em comunicação confiam no mesmo TTP;

## Certificados de Chave pública

Uma pessoa prova a uma autoridade de certificação (AC) que possui uma chave privada, através da assinatura de um pedido de certificado que contém uma chave privada usando a chave secreta ou porque a AC dá a própria chave privada. Depois a AC gera um documento eletrónico com toda a informação recolhida (tanto da origem como da própria máquina), e a validade para limitar a duração da responsabilização do contrato.
Esse documento é codificado com regras ASN1 (*Abstract Syntax Notation*) e DER (*Distinguished Encoding Rules*).

O titular da chave pública guarda o seu próprio certificado.

### Verificação de Certificados

- Validação do DNS;
- Período de validade;
- Meta-informação;
- 

#TODO