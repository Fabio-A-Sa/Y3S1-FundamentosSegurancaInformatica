# 4 - Segurança de Sistemas

## 4.1 - Princípios fundamentais de cibersegurança

- Mecanismo consistente e claro, que facilita a usabilidade e validação;
- Tratar excepções, ter defaults caso algo falhe. Utilização de "fail open", para proteção dos dados sensíveis;
- Todos os recursos sensíveis têm alguma barreira de controlo, não deve ser possível aceder diretamente;
- Desenho aberto, a arquitetura de segurança e os detalhes de funcionamento devem ser públicos (não ofuscados), porque é mais provável saber que existe vulnerabilidades.
- Separação de privilégios no sistema (utilizadores, administrador), requisitos mínimos para novos utilizadores;
- Fatores humanos;
- Mediação completa, para todo o recurso definir uma política de proteção e validar todos os acessos;

## 4.2 - Controlo de acessos

Forma de garantir que os utilizadores acedem a recursos de acordo com os seus privilégios. Para isso existem três entidades do processo:

1. Ator (entidade que realiza uma ação);
2. Recurso (sobre o qual se realiza a ação);
3. Operação (o tipo de ação que é realizada)

### 4.2.1 - Matriz de Acessos

Descreve todos os possíveis acessos para cada ator. Por um lado é uma representação clara e eficiente, por outro pode tornar-se muito grande derivado do tamanho que pode ter o sistema.

### 4.2.2 - Lista de controlo de acessos (ACL)

Cada recurso contém uma lista de permissões dos actores sobre esse próprio. Por um lado garante que um recurso pode apenas ser acedido por uma gama de atores, pois o acesso está associado ao próprio recurso, por outro não é possível determinar as permissões que um ator tem sem ver todos os recursos (por exemplo para remover um ator) nem há agregação de perfis de permissões.

### 4.2.3 - Listas de permissões (Capabilities)

Para cada ator, as operações que pode realizar sobre cada recurso. Por um lado faz-nos lidar com cada ator individualmente (armazenamento associado ao ator) e garante que um ator pode aceder a um número limitado de recursos. Por outro lado não é possível determinar todos os atores que podem manipular determinado recurso sem percorrer todos os atores nem há agregação de perfis de permissões. É pensar na lista de permissões como se fosse um bilhete para o cinema.

### 4.2.4 - Role Based Access Control (RBAC)

Usado nos sistemas Unix. Permite a ligação de perfis a conjuntos de permissões e ligação de atores a perfis. As permissões de leitura, escrita e execução (rwx) estão ligadas aos perfis de dono, grupo e outros. 

É um caso particular do ABAC, em que existe uma matriz de permissões com base nos atributos que descreve que para aceder a um recurso com atributo A o ator deve possuir o atributo B.

## 4.3 - Aplicação dos conceitos em sistemas operativos

O Sistema Operativo é a interface entre os utilizadores e o hardware. Este faz a gestão dos recursos por parte das aplicações. Mas como os atacantes podem controlar utilizadores, aplicações e usurpar permissões, pelo que tem de garantir que não há acessos abusivos. Para isso há uma série de aspectos de segurança que tem em consideração:

- Isolamento virtual entre utilizadores, aplicações e processos;
- Estar sempre a partilhar somente os recursos disponíveis no sistema;
- Aplica o privilégio mínimo, separação de privilégios;