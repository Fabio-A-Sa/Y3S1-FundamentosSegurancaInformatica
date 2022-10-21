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

### 4.3.1 - Kernel

Permite uma zona de isolamento e controlo de acessos entre processos. Há dois principais modos de operação no linux: kernel mode e user mode. A zona de ataque é a fronteira das suas camadas, ou seja, as system calls, que acedem a recursos. <br>
Tem de existir um Modelo de Confiança: confiar inicialmente no processador e no kernel instalado. 

#### Ataques

Podem ocorrer ataques em todos os níveis do boot: BIOS corrompida, bootloader corrompido, ficheiros de hibernação corrompidos. A maioria dos erros e falhas surgem de problemas de administração. <br>
A própria tradução de endereços, a busca de páginas no disco para a cache e as árvores de tradução devem permitir `kernel mapping`: quando um processo faz uma system call, não é necessário alterar o sistema de mapeamento de páginas pois a memória relevante do kernel já está mapeada e coexiste no mesmo espaço de endereçamento. 

#### Mitigação

- monitorização constante;
- assinatura de código legítimo;
- um processo não pode aceder ao espaço de memória de outro processo;
- confidencialidade, integridade e controlo de fluxo do kernel tem de ser protegida de todos os processo que executam em user mode;
- Nem o Kernel deve ser capaz de violar a regra W^X;
- namespaces, para cada processo ter visível somente alguns recursos;
- cgroups, gere a quantidade de recursos a que cada processo tem acesso;

### 4.3.2 - Sistema de Ficheiros

Cada recurso tem um owner e um grupo e as permissões são atribuídas ao owner, grupo e outros segundo a gama rwx. O superuser (sudo) tem permissões totais sobre o sistema. <br>
O `setuid` é uma forma de o utilizador X executar um programa com os privilégios do dono do ficheiro. Permite escalar privilégios, tal como vimos no Logbook 4.

Um processo tem, de facto, três UIDs:

1. Effective User ID, determina as permissões;
2. Real User ID, utilizador que lançou o processo;
3. Saved User ID: utilizado em transições, lembra sempre o anterior;

### 4.3.3 - Confinamento

Quando se executa código proveniente de fontes externas e para segurança deve ser contida. Por exemplo o javascript a correr nos browsers e sistema legacy. Para mitigar isso há monitorização e medição de todos os pedidos de acesso a recursos: criação de Jails ou containers.

#### Air Gap

Não há ligação física entre a máquina que executa o possível código malicioso e os recursos que queremos proteger. Pode ser implementado em hardware. A desvantagem é que é difícil de gerir.

#### Máquinas Virtuais

O **hypervisor** é uma camada intermédia entre o sistema operativo original e as virtualizações, oferece visão virtual de hardware a cada sistema operativo, mas ambos contidos e independentes. Muito usado em sistemas cloud.

#### SFI e SCI

SFI (Software Fault Isolation), para isolamento de processos que partilham o mesmo espaço de endereçamento, e SCI (System Call Interposition), para monitorização de pontos de acesso a operações priveligiados. 

##### SCI Racional

A superfície de ataque são as system calls. Assim, é necessário monitorizar as mesmas e bloquear as que não são autorizadas. Exemplos de soluções:

- `ptrace`, system call antes de cada system call. Monitoriza e aborta a operação caso o processo não tenha autorização a determinados recursos. Desvantagens: cada subprocesso tinha de ter outro monitor, o que era limitativo, e é vulnerável a race condition attacks.
- `seccomp`, secure computing mode, o processo quando chama **prctl** só pode retornar ou utilizar ficheiros já abertos e uma violação da regra leva o Kernel a abortar o processo. Com **bpf - berkeley packet filter** é possível configurar melhor a utilização das systems calls do processo em modo seguro. 

##### SFI 

Limitação da zona de memória acessível por cada processo, utilização de espaço de endereços diferentes e segundo o mandamento do privilégio mínimo. É importante isolar as operações load/store e jumps.

#### Sandboxing

Confinamento dentro da própria aplicação, como por exemplo nos browsers mais recentes.

#### Docker

Containers que permitem rodar programas em ambientes isolados e reproduzíveis. É mais portátil, há criação de containers por layers incrementais e permite a composição de containers préexistentes. No entanto, o deamon docker (gere containers, discos virtuais e redes) tem permissões de root.

#TODO -> Fim dos slides