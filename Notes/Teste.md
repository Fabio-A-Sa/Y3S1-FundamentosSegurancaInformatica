# Preparação para o Teste

## Tópicos

1. Introdução:
    - Motivações para os ataques;
    - Atores e atacantes;
    - Modelos de segurança;
    - Segurança e terminologias;
2. Segurança de software:
    - Overflow na stack;
    - Overflow na heap;
    - Overflow de inteiros;
    - Strings de formatação;
    - Return oriented programming;
    - Prevenções;
3. Segurança de sistemas:
    - Princípios fundamentais;
    - Controlo de acessos;

## 1. Introdução

### 1.1 - Motivações para os ataques:
 
- Acesso a credenciais de bancos e contas;
- Ransomware, pedir resgate após inutilizar as máquinas;
- Utilizar o processador como recurso para minerar bitcoins;
- Usurpar o endereço legítimo das redes para lançar SPAM ou DSoS como se fosse um utilizador normal;
- Perda de confiança global em certas empresas;
- Motivações políticas;

### 1.2 - Atores e Atacantes

Os atores são as entidades que intervêm no sistema (pessoas, organizações, empresas, máquinas), os atacantes/adversários são  

A segurança define-se sob o ponto de vista dos atores. Pode-se depositar confiança em alguns atores/componentes.

### 1.3 - Modelos de segurança

#### 1.3.1 - Modelo Binário

Caracterização formal do atacante, do sistema, e garante que o sistema é seguro com determinados ataques. Gasta-se tempo com definições, não escala para sistemas complexos, ignora side-channels.

#### 1.3.2 - Modelo de Gestão de Risco

Diminui e mitiga os riscos em função das ameaças mais prováveis. Equilibra o custo da manutenção com o custo das perdas. No entanto pode haver incorreção entre a análise dos riscos deitando tudo por terra, não há segurança total. <br>
A matriz da análise dos riscos tem dois eixos: probabilidade de ameaça com impacto no ativo (o que constui valor para o sistema).

### 1.4 - Segurança e terminologias

É um equilíbrio de CIA (confidencialidade, integridade e disponibilidade), um compromisso entre segurança, funcionalidade e usabilidade. 

- **Vulnerabilidade**,  falhas que estão acessíveis ao adversário;
- **Ameaça**, causas possíveis para um incidente que possa causar danos em ativos;
- **Exploit**, método de explorar a vulnerabilidade;
- **Ataque**, quando alguém explora a vulnerabilidade. Podem ser ativos ou passivos. A estrutura de um ataque MOM: Motivo/Ameaça, Oportunidade/Vulnerabilidade, Método/Exploit;
- **Confiabilidade**, um sistema é confiável se faz exatamente o especificado e está de acordo com um modelo de confiança e as politicas de segurança. São necessárias três etapas para o processo de confiabilidade:
    - Definir o problema, modelo de confiança e solução;
    - Validar e justificar a solução;
    - Auditoria de segurança;

## 2. Segurança de Software

### 2.1 - Overflow na Stack

Funções como `strcpy` podem copiar mais do que o buffer estático alocado, podendo reescrever endereços e injectar shellcode. A stack é constituída começa nos endereços mais elevados e cresce no sentido da heap, para endereços mais baixos. A ordem da alocação é sempre a mesma:
- frame pointer (ebp) da função que chamou;
- alocação de espaço para as variáveis locais;
- alocação de espaço para os argumentos da funço que vai chamar;
- endereço de retorno;
- stack pointer (esp);

### 2.2 - Overflow na Heap

Semelhante ao da heap, mas com exploit mais complicado pois a heap é dinâmicamente gerida e não há tanta facilidade de prever endereços. Tenta-se manipular os endereços contidos na heap, como apontadores para funções (vTables), linking dinâmico com bibliotecas, tratamento de excepções. <br>A escrita é feita através de mallocs, que escrevem e reescrevem endereços para blocos de memória. Ao exceder os limites destes blocos, podemos tentar escrever novos endereços que apontem para código malicioso.
- `Heap spraying`, quando se enche ao máximo a heap de código malicioso com NOPs para aumentar a chance de controlo;
- `Use after free`, nada garante que apontadores já free() não apontem para zonas de memória entretanto manipuladas;

## 2.3 - Overflow de inteiros

O processo de truncatura ou multiplicação pode diminuir ou até trocar o sinal do valor inicialmente colocado. Esta técnica serve para alocar um buffer demasiado pequeno (ocorre overflow) ou crash ao servidor (ocorre DoS).

### 2.4 - Strins de formatação

Funções como `printf` recorrem a parâmetros e a percentagens para ir buscar à stack os valores a mostrar. Se tiver mais percentagens que parâmetros, irá ler outros endereços crescentes da stack. O descritor `%n` acaba por escrever no endereço o número de bytes escritos para o stdout até ali.

### 2.5 - Return Oriented Programming

Na impossibilidade de injectar código na stack ou heap (devido à randomização de endereços), o que resta é reutilizar código que já está no sistema: as bibliotecas, como a libc que contém `system` e `mprotect`. <br>
Substitui-se o endereço de retorno por uma função mas antes disso configura-se a stack com o parâmetro. Utilizar também a `exit` para retornar sem dar crash e não ser detectado.

### 2.6 - Prevenções

- `DEP, Data execution prevention`, ou tem permissões de escrita ou de execução, não ambas;
- `ASLR, Address Space Layout Randomization`, até para endereços do kernel;
- `kBouncer`, que verifica rets precedidos de calls, evita ROP no entanto está limitado a 16 execuções e é apenas para system calls;
- `Stack Canaries`, com caracteres difíceis de escrever nas shell codes. Podem ser atacados com brute force ou sair sem retornar para a função anterior;
- `Memory tagging`, liga os apontadores às regiões onde apontam através de uma tag de 4 bits que só pode ser manipulada com novas instruções;
- `Control Flow Integrity CFI`, identifica pares origem-destino válidos, como num grafo;
- `Program Safety`, usando linguagens tipadas com verificação e validação matemática;

## 3. Segurança de Sistemas

### 3.1 - Princípios fundamentais

- `Economia de mecanismos`, para facilitar a implementação, usabilidade e validação;
- `Proteção por omissão`, "fail closed", se o sistema falhar impõe um nível de proteção conservador;
- `Desenho aberto`, para sabermos que estamos vulneráveis;
- `Privilégio mínimo`: cada ator deve ter apenas as permissões mínimas para desempenhar a sua função;
- `Separação de privilégios`, a utilização dos recursos deve ser isolada;
- `Mediação completa`, definir uma política de proteção para todos os recursos e validar todos os acessos;

### 3.2 - Controlo de Acessos

Aplica o privilégio mínimo, a mediação completa e a separação de privilégios através da combinação entre atores, recursos e operações. Exemplos:

- `Matriz de acessos`, cada par recurso/ator tem permissões associadas, o que é claro porém extenso de representar;
- `Lista de controlo de acessos (ACL)`, para cada recurso há uma lista das permissões e atores;
- `Lista de permissões (capabilities)`, para cada ator há uma lista de operações sobre cada recurso;
- `Role Based Access Control (RBAC)`, permite separar a gestão de recursos da gestão de atores. Os atores são agrupados em perfis (owner, group, other) e cada perfil tem determinadas permissões sobre o recurso. É exemplo o sistema Unix;
- `Attribute-based Access Control (ABAC)`, os atores e recursos têm atributos e a matriz de acessos descreve as permissões com base nesses atributos. Para aceder a recurso com atributo A o ator deve possuir atributo B. 