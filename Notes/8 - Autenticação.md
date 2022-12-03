# 8 - Autenticação

A autenticação é necessária dentro do canal seguro criado nas etapas anteriores para permitir ações. Para isso, gera-se aleatoriamente no momento um componente (timestamp ou outro) de tempo de vida muito curto e envia-se à entidade que se quer autenticar. A entidade terá de cifrar o componente e enviar, provando que está ativo naquele momento.

### Provas de identidade

Podem ser usadas individualmente ou em multi-factor. Exemplos:

- Algo que se sabe (password, PIN);
- Algo que se possui (smartcard, telemóvel);
- Algo que se é intrinsecamente (biometria);

## Passwords

Por um lado é uma forma simples de autenticação, mas por outro lado há uma série de problemas associados:
- A pessoa revela a password a terceiros, diretamente ou por **phishing**;
- Ataques a *endpoints*, keyloggers, clipboards;
- Na rede, ao não usar a ligação TLS;
- Ataques  ao servidor que contém a base de dados;

### Armazenamento das Passwords

Não necessitamos de guardar a password explicitamente, só necessitamos de a reconhecer. Nos servidores normalmente grava-se um hash(password) ou pares salt-hash(salt + password). Neste último caso inibe ataques por dicionário, uma vez que as mesmas passwords em diversos sítios darão resultados distintos. <br>
Para atrasar o brute-force, pode ser necessário usar funções de hash mais lentas, como o *bcrypt*.

## Autenticação Multifactor

Defesa em profundidade no caso da password ser quebrada. Como segundo fator usa-se um token no telemóvel, smartcard (não têm interface com o utilizador), One-Time Token (com interface, mas limitada, gera MACs da hora atual com criptografia simétrica).

### One-Time Token

Com interface, mas limitada. Gera MACs da hora atual com criptografia simétrica. Por um lado é de fácil utilização, mas por outro não inibe Man-in-the-middle Attacks e o servidor necessita de armazenar chaves secretas (problemas de escalabilidade e de ponto único de falha).

### One-Time Passcode

De utilização mais recente. Consiste no envio de tokens virtuais para um dispositivo próximo à pessoa (SMS ou email), fazendo *challenge-response*. As chaves são mais fáceis de gerir, mas há menos independência entre fatores (telefone roubado, por exemplo). 