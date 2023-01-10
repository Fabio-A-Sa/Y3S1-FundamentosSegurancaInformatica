# Preparação para o Exame

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
    - Sistemas operativos;
    - Permissões;
    - Confinamento;
4. Segurança da Web:
    - Protocolo HTTP;
    - SOP;
    - CORS;
    - Ataques;

## 1. Introdução

### 1.1 - Motivações para os ataques:
 
- Acesso a credenciais de bancos e contas;
- Ransomware, pedir resgate após inutilizar as máquinas;
- Utilizar o processador como recurso para minerar bitcoins;
- Usurpar o endereço legítimo das redes para lançar SPAM ou DSoS como se fosse um utilizador normal;
- Perda de confiança global em certas empresas;
- Motivações políticas;

### 1.2 - Atores e Atacantes

Os atores são as entidades que intervêm no sistema (pessoas, organizações, empresas, máquinas), os atacantes/adversários são os que tentam atacar os recursos anteriormente descritos.<br>
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
- frame pointer (ebp) da função que chamou a atual;
- alocação de espaço para as variáveis locais;
- alocação de espaço para os argumentos da função que vai chamar;
- endereço de retorno, para quando sair da função que vai chamar conseguir voltar para o mesmo ponto de execução;
- stack pointer (esp);

### 2.2 - Overflow na Heap

Semelhante ao da heap, mas com exploit mais complicado pois a heap é dinâmicamente gerida e não há tanta facilidade de prever endereços. Tenta-se manipular os endereços contidos na heap, como apontadores para funções (vTables), linking dinâmico com bibliotecas, tratamento de excepções. <br> A escrita é feita através de mallocs, que escrevem e reescrevem endereços para blocos de memória. Ao exceder os limites destes blocos, podemos tentar escrever novos endereços que apontem para código malicioso.
- `Heap spraying`, quando se enche ao máximo a heap de código malicioso com NOPs para aumentar a chance de controlo;
- `Use after free`, nada garante que apontadores já free() não apontem para zonas de memória entretanto manipuladas;

## 2.3 - Overflow de inteiros

O processo de truncatura ou multiplicação pode diminuir ou até trocar o sinal do valor inicialmente colocado. Esta técnica serve para alocar um buffer demasiado pequeno (ocorre overflow) ou crash ao servidor (ocorre DoS).

### 2.4 - Strings de formatação

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

### 3.3 - Sistemas operativos

Interface entre os utilizadores e o hardware. Garante isolamento de processos, utilizadores, partilha de recursos nas duas camadas: user mode e kernel mode. Os pontos de entrada para os ataques são as system calls e os daemons, que são processos que correm em segundo plano. Existem, para isso, várias medidas de mitigação:
- Monitorização constante;
- Mediação, por meio de assinaturas digitais;
- Isolamento entre processos;
- Kernel mapping, parte da memória do kernel já está mapeada na memória do processo com permissões diferentes para não ocorrer um page-fault e não ir à memória não volátil;
- Defesa em profundidade, nem o Kernel pode violar W^X;

O kernel atual já possui **namespaces**, para controloar os recursos visíveis, e **cgroups**, para controlar a quantidade de recursos utilizados por um processo e a sua prioridade.

### 3.4 - Permissões

Existem três tipos de UIDs:
1. `Effective User ID`, determina as permissões;
2. `Real User ID`, utilizador que lançou o processo;
3. `Saved User ID`: utilizado em transições, lembra sempre o anterior;
Um superuser tem o poder de criar processos, baixar e elevar as suas permissões usando **setuid**. O **seteuid** apenas altera o EUID. Os outros utilizadores têm o poder de baixar as suas permissões, mudando apenas EUID para RUID ou SUID. 

### 3.5 - Confinamento

- `Air Gap`, o código malicioso não afeta o sistema, pois este está desligado devido ao hardware. No entanto é difícil de gerir;
- `Máquinas virtuais`, o hypervisor permite partilha de hardware. Existe o tipo 1, usado nas clouds que contém um SO fino sobre o hardware, ou tipo 2, usado nos computadores pessoais que contém uma camada de software sobre o SO;
- `Software Fault Isolation (SFI)`, para isolamento de processos que partilham o mesmo espaço de endereçamento, como o isolamento de memória de endereçamento;
- `System Call Interposition (SCI)`, para monitorização de pontos de acesso a operações previligiados, como a separação do user-mode do kernel mode, operações de jump/load/store;
    - `ptrace`, system call antes de cada system call. Monitoriza e aborta a operação caso o processo não tenha autorização a determinados recursos. Desvantagens: cada subprocesso tinha de ter outro monitor, o que era limitativo, e é vulnerável a race condition attacks.
    - `seccomp`, secure computing mode, o processo quando chama **prctl** só pode retornar ou utilizar ficheiros já abertos e uma violação da regra leva o Kernel a abortar o processo. Com **bpf - berkeley packet filter** é possível configurar melhor a utilização das systems calls do processo em modo seguro. 
- `Sandboxing`, confinamento dentro da própria aplicação, como os browsers em relação ao javascript;
- `Containers`, partilham o kernel do sistema operativo e isolam os componentes em user mode. Isto implica ter menos recursos disponíveis;

Um software pode detectar que está a correr numa máquina virtual:
- Instruções disponíveis;
- Latência no acesso à memória cache;
- O hypervisor utiliza parte dos mecanismos de gestão do HW pelo que pode detectar a limitação de recursos;

## 4. Segurança da Web

Atualmente podemos ter atacantes no browser (injecta malware no browser), atacantes externos (controla o meio de comunicação), atacantes internos (controla o servidor, uma página do cliente ou um componente da página).

### 4.1 - Protocolo HTTP

Protocolo sem estado que tem vários pedidos disponíveis, de entre os quais:
- GET, obter recurso sem side-effects;
- POST, criar um novo recurso com side-effects;
- PATCH, alterar parte do recurso;
- PUT, substituir recurso;
- DELETE, remover recurso;
A resposta tem um status code, headers e pode ou não ter body, com HTML ou JSON.

### 4.2 - Same Origin Policy (SOP)

Modelo de segurança que só deixa frames comunicarem entre si se tiverem a mesma origem (esquema, nome de domínio e porta). 

**Para pedidos ao servidor:**<br>
A resposta de qualquer origem é processada, criação de frame e javascript dessa origem, mas não pode haver manipulação dessa informação por parte de outro script.

**Para cookies:**<br>
A definição de origem é diferente: é apenas o domínio e path. Aqui as cookies são enviadas quando o seu domínio for um sufixo do domínio da URL e se a sua path for um prefixo da path do URL. Para os novos sites, se SameSite=None, então enviam as cookies de qualquer forma. De forma simplificada, o Path da cookie deve estar contido do URL que o pede, podendo até ser iguais.

### 4.3 - Cross-Origin Resource Sharing (CORS)

Permite relaxar o SOP. O servidor permite ao Browser recolher também os recursos de outros servidores através de flags aquando dos pedidos. Podem existir pedidos simples, em que a permissão vem no protocolo HTML, ou pre-flighted, quando existe um primeiro pedido dummy que vai verificar se é possível um servidor externo X dar o recurso solicitado.

### 4.4 - Ataques

#### 4.4.1 - Cross Site Request Forgery (CSRF)

Quando há usurpação de cookies (*session hijacking*) pedidos com side-effects podem ser efetuados. `XS-Leaks` é algo semelhante, requests em nome do utilizador onde são monitorizados os erros e contando os frames para extrair informação. <br>
A prevenção é criar um CSRF token para em forms críticos/com side-effects.

#### 4.4.2 - Insecure Direct Object Reference (IDOR)

Quando não há controlo de acessos ou quando estes são limitados. 

#### 4.4.3 - SQL Injection

Acontecem quando o input não é validado e não há uma separação clara entre os dados e o tratamento dos mesmos pelo sistema. Devemos sanitizar os inputs. Os inputs não devem ser parte do comando mas sim parâmetros injectados em formato de texto. Nunca criar comandos SQL dinamicamente como strings.

#### 4.4.4 - Cross-Site Scripting (XSS)

Injeção de código por parte do cliente:
- `reflected`, quando a máquina da pessoa atacada serve de meio para retirar informação dos servidores;
- `stored`, quando o ataque é permanente, colocado no servidor ou base de dados;

É importante validar os inputs, ou fazer CSP (*content security policy*), que garante que scripts in-line não são executados, a menos que estejam na white-list.

#### 4.4.5 - Security Misconfiguration

O utilizar vê um frame do site alvo mas a interação é com o site malicioso

#### 4.4.6 - Insecure Design

O cliente não pode confiar no servidor (XSS, por exemplo, ou armazenamento das credienciais dos utilizadores de forma insegura), e o servidor não pode confiar no cliente (inputs inválidos, inputs negativos, gerar mensagens de erros com informação sensível, como versões ou logs).