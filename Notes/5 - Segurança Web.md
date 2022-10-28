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

Cada frame tem uma origem (com esquema, nome do domínio e porta). Só frames/iframes da mesma origem é que vão poder comunicar entre si. 

#TODO -> Até ao fim dos slides

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



### Injection

Acontecem quando o input não é validado e não há uma separação clara entre os dados e o tratamento dos mesmos pelo sistema. 

#### Servidores CGI

#### SQL Injection

#### Cross-Site Scripting (XSS)

