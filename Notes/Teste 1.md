# Preparação para o teste 1

## Tópicos

1. Introdução:
    - Motivações para os ataques;
    - Atores e atacantes;
    - Modelos de segurança;
    - Segurança e terminologias;
2. Segurança de software:
    - Overflow na stack;
    - Overflow na heap;
    - Integer overflow;

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

