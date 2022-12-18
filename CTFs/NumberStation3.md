# NumberStation3 - CTF Extra

Inicialmente exploramos o ficheiro disponibilizado na plataforma CTF que é o mesmo que está a ser executado no servidor na porta 6002. O valor dado como output pelo servidor é de facto a flag encriptada:

```note
encoded_flag = 
"feb24f6a8b12b1aa7886e5941803191eea64c3232587a73d3ab352bff185f4aaacb029205daafdddc71803df502901378bd5e67bb1fc9887989121728a067ca8d342019
8c15571a57776b9c8b035c591595859bf5c01f6a752701413e697eca4dd5cd1f6f2cc1e39654f028668c65b7e75b170ded8d1152deae26e654b68e8a79cb311b309b9462
31622ae88fc2e25e2e46886d6bd086a28fe74b9aabac2e41675b170ded8d1152deae26e654b68e8a75e0ed48319c66cee27161bc79f1e028787fdbf150247108f516c7a9
6bae6db917a4a5aa98554acd6f4548a14590143a95e0ed48319c66cee27161bc79f1e028787fdbf150247108f516c7a96bae6db915e0ed48319c66cee27161bc79f1e028
790c5c42c5b8d35c3d3ed263e7479769c90c5c42c5b8d35c3d3ed263e7479769c44a208a73a85a6d558efc0aef8bf28de90c5c42c5b8d35c3d3ed263e7479769ce46886d
6bd086a28fe74b9aabac2e41644a208a73a85a6d558efc0aef8bf28de121abda046c59c1caad434555abc4247df60e274e8452a70cc7915ad358b1155e7cfe1ab88c69bb
564a7f6aef803e4929cb311b309b946231622ae88fc2e25e244a208a73a85a6d558efc0aef8bf28dee46886d6bd086a28fe74b9aabac2e41690c92623de59c4395272b1b
ea7ad903d90c92623de59c4395272b1bea7ad903de46886d6bd086a28fe74b9aabac2e4169cb311b309b946231622ae88fc2e25e2121abda046c59c1caad434555abc424
7df60e274e8452a70cc7915ad358b115544a208a73a85a6d558efc0aef8bf28de75b170ded8d1152deae26e654b68e8a7cc6863fa2e1072d4a5ffc76beb7164ec17f703b
d57be978179f23fcdb5208e8d"
```

Olhando mais atentamente para a estrutura do código, percebemos o funcionamento do método de encriptação usada. Utiliza-se o bloco AES com uma chave aleatória gerada em *runtime*:

```python
def gen(): 
	rkey = bytearray(os.urandom(16))            # Gera um número aleatório
	for i in range(16): rkey[i] = rkey[i] & 1   # Cada um dos elementos do array é um bit (0, 1)
	return bytes(rkey)                          # Retorna o array de bytes correspondente

def enc(k, m):
	cipher = Cipher(algorithms.AES(k), modes.ECB())
	encryptor = cipher.encryptor()
	cph = b""
	for ch in m:
		cph += encryptor.update((ch*16).encode())
	cph += encryptor.finalize()
	return cph
```

A chave embora seja randomizada é de uma estrutura fixa e de tamanho conhecido: 16 bits. Ou seja, neste sistema existem somente 2^16 possíveis chaves. Como esse valor ainda está dentro de uma gama aceitável para o processamento podemos recorrer a *brute force* para gerar todas as possibilidades de chave e fazer o processo inverso usando a função `dec(k, m)` disponibilizada:

```python
def dec(k, c):
	assert len(c) % 16 == 0
	cipher = Cipher(algorithms.AES(k), modes.ECB())
	decryptor = cipher.decryptor()
	blocks = len(c)//16
	msg = b""
	for i in range(0,(blocks)):
		msg+=decryptor.update(c[i*16:(i+1)*16])
		msg=msg[:-15]
	msg += decryptor.finalize()
	return msg
```

Primeiro precisamos de construir uma função que dado um valor `number` na gama [0, 2^16] gere o array de bytes correspondente. Usamos esta função:

```python
def generateKey(number):                                  # Ex: number = 1287
	bits = [ number >> index & 1 for index in range(16) ] # Ex: [1, 1, 1, 0, 0, 0, 0, 0, 1, 0, 1, 0, 0, 0, 0, 0]
	return bytes(bits)                                    # Ex: b'\x01\x01\x01\x00\x00\x00\x00\x00\x01\x00\x01\x00\x00\x00\x00\x00'
```

Finalmente implementamos o *brute force*:

```python
for attemp in range(0, 2**16):
	k = generateKey(attemp)
	undo = dec(k, unhexlify(encoded_flag))
	print(undo)
```

O script gerado até então irá calcular e mostrar o resultado de todas as 65536 (2^16) tentativas. Sabemos desde o princípio que as flags são da forma `flag{...}`. Para encontrar o resultado pretendido no meio de milhares de linhas de output bastou usar uma `pipe` e `grep` de Linux:

![Flag](../img/numberstation.png)

Tal como previsto uma das chaves geradas foi capaz de converter a criptografia usada e mostrar a mensagem original. O script com todo o código necessário para completar o CTF está disponível em [NumberStation3](/CTF/Exploits/NumberStation3.py).