# 7 - Public-Key Infrastructure (PKI)

## Certificados de Chave pública

É necessário provar a autenticidade do par chave pública e chave privada. Para isso usa-se um canal autenticado TTP (*Trusted-Third-Party*) que reune conhecimento sobre chaves e que pode certificar se determinada chave pertence a determinada entidade. No entanto há problemas de:
- Construção do canal de comunicação com o TTP;
- Estado offline;
- Como garantir que as duas entidades em comunicação confiam no mesmo TTP;

### Certificados

Documento codificado com regras ASN (*Abstract Syntax Notation*) e DER (*Distinguished Encoding Rules*).

O titular da chave pública guarda o seu próprio certificado.

### Verificação de Certificados

- Validação do DNS;
- Período de validade;
- Meta-informação;
- 

#TODO