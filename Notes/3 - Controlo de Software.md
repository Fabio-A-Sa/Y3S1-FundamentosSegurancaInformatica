# 3 - Controlo de Software

O software pode ser manipulado para causar dano nos sistemas. É importante estudar vulnerabilidades e ataques anteriores, para blindar o novo código de ser controloado externamente. Chama-se `usurpação de controlo` quando um input externo leva o programa a quebrar a sequência de instruções que está idealizada

## 3.1 - Modelo de memória

Do endereço menor para o endereço maior temos:

- **Text**, onde tem o código a executar;
- **Data**, onde teo os dados globais e estáticos;
- **Heap**, armazena a memória dinamicamente;
- **Stack**, armazena a informação como endereços de retorno, variáveis locais e argumentos para as funções;

<p align="center">
    <img src="../Images/Memory.png">
    <p align="center">Figura 1: Esquema de memória</p>
</p>

### 3.1.1 - Funcionamento da Stack

A stack cresce de cima para baixo, com a função main() no topo. Cada chamada a funções regista na stack o endereço de retorno, as variáveis locais e os parâmetros da função a chamar. A frame é delimitada pelo frame pointer da função anterior e o stack pointer. Antes do stack pointer existe o return address. 

## 3.2 - Stack Smashing / Buffer overflow na stack

Ataque documentado desde 1972. Há funções como o **strcmp** que copia uma string para um apontador de variável local até ocorrer um `\0`. Ou seja, poderá corromper o frame pointer anterior e o endereço de retorno. Erros deste tipo normalmente resultam em segmentation fault, quando um processo está a escrever fora da sua zona de memória. No entanto, o endereço de retorno pode ser manipulado para apontar para um código do atacante (por exemplo, um código bash "shell code").

### 3.2.1 - Shell Code

- Codificado em código máquina;
- Sequência de instruções que executam instruções shell;
- Precedido por instruções NOP porque o seu endereço pode variar e assim há mais probabilidades de acertar;
- Um problema é não conter \0 no código, pois o strcmp pára assim que encontrar este caracter e pode estragar o código injectado;

### 3.2.1 - Off-by-one

Um byte é escrito fora da variável local, logo permite escrever o byte menos significativo do frame pointer anterior.

## 3.3 - Overflows na Heap

Pode também haver endereços alocados na Heap, através de:
- virtual functions (há uma tabela de apontadores na Heap)
- programa que depende de bibliotecas do sistema. Em runtime essas dependências são carregadas para a memória e para isso é necessário existir endereços;
- tratamento das excepções;

O armazenamento das estruturas da heap são maioritariamente efetuadas por listas ligadas. São exemplos as operações malloc/free. Assim, basta tentar exceder a capacidade de escrita de uma unidade para tentar reescrever para onde aponta.

Apesar de ser muito mais complexo definir apontadores na heap, já que é gerida dinamicamente ao contrário da stack, o exploit pode ser conseguido.

### 3.3.1 - Heap Spraying

Os browsers correm código do servidor (possivelmente malicioso) no lado do utilizador. O código poderá alocar dinamicamente o máximo de scripts maliciosos com nops para ter mais probabilidade de ser executado.

### 3.3.2 - Use After Free

Em linguagens de baixo nível como C/C++, um código malicioso pode ser alocado e, mesmo depois de um free(), poder ser executado com endereços escolhidos pelo atacante. O código pode chamar um método de uma instância destruída sem ocorrer um erro no sistema.

## 3.4 - Overflows de Inteiros

A truncatura por passagem para tipo mais pequeno ou uma adição/multiplicação poderá provocar uma troca de sinal (o inteiro passa de positivo para negativo). Assim há erro no tamanho das estruturas a alocar, o que pode provocar erros ou buffer overflow. 

## 3.5 - Strings de Formatação

Por exemplo na função printf(). Os parâmetros (um por cada %), são alocados na stack por ordem decrescente de utilização. No entanto, se houver argumentos a mais, acaba por imprimir dados sensíveis, como algo apontado por endereços na stack. 

```c
printf("<endereço>%d%d%d%d%d%s)
```

O código anterior permite ler o conteúdo da zona de memória apontada por um endereço arbitrário, já que a própria string de formatação está na stack. <br>
Existe também uma hipótese de formatação com a `sprintf()` e o radical `%n`, que escreve numa zona arbitrária de memória a quantidade, num inteiro, de bytes que já escreveu. Assim é possível escrever endereços completos por parte do atacante. 

### Técnicas de mitigação

Nos sistemas mais modernos, existem algumas técnicas para mitigar as vulnerabilidades anteriores:

- Uma página de memória executável não pode ser escrita;
- Uma página de memória com permissões de escrita não podem ser executáveis;
- Randomização do espaço de endereços;

## Ataques usando Bibliotecas

Em alternativa, os ataques podem ocorrer sem injeção de código malicioso: usando as bibliotecas do próprio sistema operativo, como a famosa `libc`, pois tem incorporadas funções de chamada ao sistema (*system*) e que alteram as permissões de memória (*mprotect*). Os seus endereços são mais fáceis de prever.

A ideia dos ataques deste tipo é substituir o endereço de retorno da função atual pelo endereço de uma função da libc e configurar a stack com os parâmetros que essa função necessita. Para evitar um crash, usar a função da libc exit(0). São sempre exploits complicados porque é difícil um endereço não conter \0.

### Return Oriented Programming

Se uma função não receber parâmetros, a função que a chama apenas precisa de armazenar na stack o endereço de retorno. Assim dá para encadear execuções de funções, basta colocar na stack consecutivos endereços de retorno.

Este caso continua a funcionar se a última função desta cadeia for a única a receber parâmetros. Para encadear funções com parâmetros entre si podemos usar pedaços de código em assembly que dão pop() ou modificam endereços da stack que não interessam entre os argumentos necessários para o ataque. 

# Segurança no controlo de Software

