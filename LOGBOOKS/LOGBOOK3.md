# Moodle 3.6.1 - Persistent Cross-Site Scripting (XSS)

## Identificação

- A vulnerabilidade [CVE-2019-3810](https://www.exploit-db.com/exploits/49814) foi explorada através de um servidor PHP que dava suporte ao Moodle na versão 3.6.1.
- Através de XSS (injeção de um script persistente e malicioso na base de dados do servidor), há modificação de privilégios, onde o utilizador consegue passar de estudante para administrador dentro do sistema.

## Catalogação

- Foi catalogada em abril de 2021 por Fariskhi Vidyan ("farisv").
- Este bug foi descoberto durante a realização de testes de penetração numa instituição académica.
- Está avaliado com um grau de gravidade baixo, e um CVSS Score de 5.0/10.0.

## Exploit

- Os administradores do sistema Moodle (e apenas estes) têm acesso as fotos de perfil de todos os utilizadores da plataforma através do endereço http://www.yourmoodlesite.org/userpix/.
- Nesta página, os nomes completos dos utilizadores eram incluidos como texto quando se passava com o rato sobre as suas imagens de perfil, o que permitia executar código previamente inserido nos campos de primeiro e último nome do utilizador.
- Isto permite executar um código que torna o utilizador num administrador.
- Não existe nenhum módulo no metasploit relacionado com esta vulnerabilidade.

## Ataques

- Não houve registo de ataques, visto que a vulnerabilidade foi descoberta através de testes de penetração e inmediatamente resolvida.
- Um potencial ataque permitiria um utilizador obter acesso a diversas funções admnistrativas da plataforma.

### Fontes consultadas

- https://www.cvedetails.com/cve-details.php?t=1&cve_id=CVE-2019-3810
- https://github.com/farisv/Moodle-CVE-2019-3810
- https://moodle.org/mod/forum/discuss.php?d=381230#p1536767
- https://www.cvedetails.com/cve-details.php?t=1&cve_id=CVE-2019-3810