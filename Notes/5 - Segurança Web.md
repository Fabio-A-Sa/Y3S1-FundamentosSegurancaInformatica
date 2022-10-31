# 5 - Segurança Web

## Protocolo HTTP

Permite a um cliente aceder a recursos do lado do servidor. Um recurso é definido por um URL, que contém:

- esquema (http, https)
- domínio (www.sigarra.up.pt)
- caminho (/feup)
- query e/ou fragmento (?ano_letivo=2022#objectivos)

O protocolo HTTP não tem estado/memória de pedidos anteriores do utilizador. 

### Pedidos 

Podem ser GET (sem side-effects), POST (com side-effects, para alterar uma base de dados por exemplo), PUT (substutuir a representação de um recurso), PATCH (alterar parte do recurso), DELETE (para apagar um recurso numa URL).

### Respostas

Status code, headers e body (código HTML ou JSON).

## Modelo de Execução

Uma janela do browser pode ter conteúdo de várias origens: frames (divisão rígida) e iframes (divisão dinâmica num mesmo documento). Estes últimos permitem isolamento entre elementos.

O Javascript pode alterar o DOM do documento HTML dinamicamente.

## Modelos de Ataque

- Atacante no browser
- Atacante externo/na rede
- Atacante interno (utilizador)
- Atacante web (servidor)

### SOP - Same Origin Policy

Cada frame tem uma origem (com esquema, nome do domínio e porta). Só frames/iframes da mesma origem é que vão poder comunicar entre si.  <br>
Para pedidos ao servidor:

- Podemos criar frames com código HTML de outras origens, mas não podemos inspecionar ou modificar o conteúdo da frame;
- Podemos obter e executar scripts de outras origens, mas esse código não pode manipular outro de javascript de outra origem;
- O browser pode fazer rendering de imagens, CSS e fontes, mas o SOP proibe modificar píxeis ou outros parâmetros;

#### Cookies

Os browsers enviam todas as cookies no contexto de uma URL (diretamente no pedido) se:

- o domínio da cookie for um sufixo do domínio da URL;
- a path da cookie for um prefixo da path da URL;

Na resposta HTTP em que o servidor fornece a cookie, o atributo **SameSite** pode ser strict (para a mesma origem), lax (o default atual, para distinguir alguns pedidos). As cookies seguras são aquelas enviadas por HTTPS.

### CORS - Cross-Origin Resource Sharing

Permite relaxar o SOP. O servidor permite ao Browser recolher também os recursos de outros servidores através de flags aquando dos pedidos. 

## Ataques

### Broken Access Control

#### Cross-Site Request Forgery (CSRF)

Como o servidor não sabe a origem do pedido, quem tem acesso à cookie consegue fazer pedidos. A autenticação por cookies não é suficiente para pedidos com side-effect (POST, por exemplo). <br>
Não estão limitados a cookies, podem ser em routers.

A prevenção garante-se com um CSRF Token dinâmico, mandando-o em cada pedido com side-effects e no momento de validação no servidor rejeita o pedido caso o token seja diferente.

#TODO -> Cont

#### Cross-Site Leaks

Atques mesmo para pedidos sem side-effects no servidors, monitorando os eventos de erro medindo as respostas contando o número de frames.

#### Insecure Direct Object Reference (IDOR)

Quando não há controlo de acessos ou quando estes são limitados. 

### Injection

Acontecem quando o input não é validado e não há uma separação clara entre os dados e o tratamento dos mesmos pelo sistema. 

#### SQL Injection

Sanitizar os inputs. Os inputs não devem ser parte do comando mas sim parâmetros injectados em formato de texto. Nunca criar comandos SQL dinamicamente como strings.

#### Cross-Site Scripting (XSS)

Ignora o SOP, pois quem corre o código malicioso é o próprio cliente. O atacante consegue convencer o cliente a correr código malicioso no seu browser para um site legítimo, para que este possa ver a resposta. Existem dois tipos de XSS:

- Reflected, quando a máquina da pessoa atacada serve de meio para retirar informação dos servidores;
- Stored, quando o ataque é permanente, colocado no servidor ou base de dados;

Uma forma de prevenção de XSS é sempre validar os inputs, headers, cookies. Os mecanismos proporcionados por frameworks ainda são incompletos. Outra solução é usar Content Security Policy (CSP) para o site permitir ou não execução de scripts, dos seus próprios scripts ou de scripts que estejam numa white-list. 

#### Insecure design

O cliente não pode confiar no servidor (XSS, por exemplo, ou armazenamento das credienciais dos utilizadores de forma insegura), e o servidor não pode confiar no cliente (inputs inválidos, inputs negativos, gerar mensagens de erros com informação sensível, como versões ou logs).