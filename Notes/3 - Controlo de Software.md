# 3 - Controlo de Software

O software pode ser manipulado para causar dano nos sistemas. É importante estudar vulnerabilidades e ataques anteriores, para blindar o novo código de ser controloado externamente. Chama-se `usurpação de controlo` quando um input externo leva o programa a quebrar a sequência de instruções que está idealizada

## 3.1 - Modelo de memória

Do endereço menor para o endereço maior temos:

- Text, onde tem o código a executar;
- Data, onde teo os dados globais e estáticos;
- Heap, armazena a memória dinamicamente;
- Stack, armazena a informação como endereços de retorno, variáveis locais e argumentos para as funções;

<p align="center">
    <img src="../Images/Memory.png">
    <p align="center">Figura 1: Esquema de memória</p>
</p>

### 3.1.1 - Funcionamento da Stack

A stack cresce de cima para baixo, com a função main() no topo. Cada chamada a funções regista na stack o endereço de retorno, as variáveis locais e os parâmetros da função a chamar. 