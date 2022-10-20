# Environment Variable and Set-UID Program Lab

## Tasks

### Task 1
> Todas as variáveis do sistema podem ser listadas a partir do comando `printenv`. Ao escrever o comando `printenv ENV`, há somente listagem das variáveis contidas no ambiente ENV. Por exemplo: <br>
> ````bash
> $ printenv PWD # /home/seed
> ````

 ### Task 2
> Guardando em dois ficheiros diferentes as variáveis de ambiente do processos pai (parentenv.txt) e filho (childenv.txt), ao fazer a diferença entre os 2, o resultado é vazio: <br>
> ![](../img/lab4task2.png) <br>
> Assim, conclui-se que todas as variáveis de ambiente do processo pai são heredadas pelo filho após o fork(), ou seja, não há diferença no ambiente de execução. <br>


### Task 3
> - Ao executar o código do ficheiro "myenv.c" com o terceiro parâmetro do comando "execve" a NULL, as variáveis de ambiente não ser passadas a esse apontador visto que tem valor nulo, originado assim um output vazio. <br>
> - No caso de substituirmos NULL pela variavel "environ", este *array* será usado para passar as variáveis de ambiente. <br>


### Task 4
> - Fazendo uso do comando "system()", verifica-se que é criado um novo processo onde são passadas todas variaveis de ambiente do processo anterior. <br>
> - Já a chamada do "execve()" executa o comando mantendo o processo atual e as mesmas variáveis de ambiente.ubstitui o processo em execução pelo comando passado como argumento, que so mantem as variaveis de ambiente se estas forem passadas como argumento. <br>

### Task 5
> - Set-UID é um mecanismo no sistema de permissões dos ficheiros em UNIX que permite um utilizador executar um programa/ficheiro com as permissões do criador desse programa/ficheiro.<br>
> - Criou-se um programa que mostra todas as variáveis de ambiente do processo atual  <br>
> - Definiu-se 'root' como proprietário do programa e tornou-se o mesmo num programa Set-UID, atraves dos seguintes comandos:  <br>
> ````bash
> $ sudo chown root setUID 
> $ sudo chmod 4755 setUID 
> ````
> - Por motivos de teste, foram realizadas alterações em algumas variáveis de ambiente:<br>
> ````bash
> $ export PATH=$PATH:/home/seed/Desktop
> $ export LD_LIBRARY_PATH=/home/seed/myScripts/
> $ export COURSE_NAME=FSI
> ````
> - De seguida, o programa foi executado novamente e verificou-se que as variáveis definidas anteriormente constavam na lista de variáveis de ambiente do programa, exceto LD_LIBRARY_PATH. Isto acontece porque LD_LIBRARY_PATH permite definir um caminho onde o programa vai procurar as bibliotecas dinamicas partilhadas, o que iria permitir a execução de um programa malicioso através da substituição de uma das bibliotecas utilizadas. <br>

### Task 6
> - Criou-se um programa Set-UID com o seguinte que chama a utiliza o comando `ls` do Linux:
> ```c 
>  system("ls");
> ```
> - Criou-se um programa 'malicioso' com o nome `ls` no diretório `/home/seed/Desktop/fakeLs`
> - Mudou-se a variável de ambiente PATH para o diretório com o programa 'malicioso':
> ```bash
> $ export PATH=/home/seed/Desktop/fakeLs
> ```
> - Verificou-se que a função `ls` executada foi a maliciosa e não a do sistema operativo.
