# Moodle 3.6.1 - Persistent Cross-Site Scripting (XSS)

## Identificação

A vulnerabilidade [CVE-2019-3810](https://www.exploit-db.com/exploits/49814) foi explorada através de um servidor PHP que dava suporte ao Moodle na versão 3.6.1. <br>
Através de XSS (injeção de um script persistente e malicioso na base de dados do servidor), há modificação de privilégios, onde o utilizador consegue passar de estudante para administra
dor dentro do sistema. 

## Catalogação

Foi catalogada em abril de 2021 por "farisv". 
explicar como funciona

## Exploit

descrever que tipo de exploit é conhecido e que tipo de automação existe, e.g., no Metasploit (max 4 itens com 20 palavras cada)

## Ataques

 descrever relatos de utilização desta vulnerabilidade para ataques bem sucedidos e/ou potencial para causar danos (max 4 itens com 20 palavras cada)


### Fontes consultadas



### Grupo L05G05

- Alexandre Guimarães Gomes Correia (up202007042@fe.up.pt);
- Fábio Araújo de Sá (up202007658@fe.up.pt);
- Lourenço Alexandre Correia Gonçalves (up202004816@fe.up.pt);