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

Descreve todos os possíveis acessos para cada ator. Por um lado é uma representação clara e eficiente, por outro pode tornar-se muito grande derivado do tamanho do sistema.

### 4.2.2 - Lista de controlo de acessos

#TODO


