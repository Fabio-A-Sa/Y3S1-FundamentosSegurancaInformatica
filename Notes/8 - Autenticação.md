# 8 - Autenticação

A autenticação é necessária dentro do canal seguro criado nas etapas anteriores para permitir ações. Para isso, gera-se aleatoriamente no momento um componente (timestamp ou outro) de tempo de vida muito curto e envia-se à entidade que se quer autenticar. A entidade terá de cifrar o componente e enviar, provando que está ativo naquele momento.

### Provas de identidade

Podem ser usadas individualmente ou em multi-factor. Exemplos:

- Algo que se sabe (password, PIN);
- Algo que se possui (smartcard, telemóvel);
- Algo que se é intrinsecamente (biometria);

## Passwords

