# CTF 6 - Semana 11

## Primeira parte

Inicialmente exploramos o ficheiro disponibilizado na plataforma CTF. Trata-se de um template para o *encode* e *decode* de mensagens com criptografia RSA:

```python
p = # next prime 2**512
q = # next prime 2**513
n = p*q
e = 0x10001 # a constant
d = # a number such that d*e % ((p-1)*(q-1)) = 1

def enc(x):
	int_x = int.from_bytes(x, "big")
	y = pow(int_x,e,n)
	return hexlify(y.to_bytes(256, 'big'))

def dec(y):
	int_y = int.from_bytes(unhexlify(y), "big")
	x = pow(int_y,d,n)
	return x.to_bytes(256, 'big')
```

No servidor dos CTFs, na porta 6000, foi possível observar a mensagem em hexadecimal a decifrar:

```note
encoded_flag = "adc74cd3abde54a74c2daa7ceb43e4526a17ed3b8c231e9fe37c6041a3c13861f1fbf9d2b919ec672bed853
                90d7039accc053274cfc976fcbd1f826e8af67dbfdd9ada20eb7a8edb6e14745f5cba60c35cb9a9e304552d
                2e3b4dcdb46568a1796f7093b39b078bab53b88c03cf43ff546ec1f8cd82062141656d18a6c23e6ed4"
```

Tal como o ficheiro sugere foi necessário primeiro encontrar os valores de `p` e `q`, ambos primos. Sabia-se que eram próximos de 2^512 e 2^513, respetivamente, pelo que foi necessário criar uma função para encontrá-los em modo incremental. No entanto o nosso método de verificar se um número era primo (*is_prime(n)*) era pouco eficiente e muito demorado para esta gama de valores. <br>
Decidimos pesquisar sobre o assunto e encontramos o algoritmo de Miller–Rabin também adaptado para a linguagem Python. O código usado foi extraído [desta fonte](https://rosettacode.org/wiki/Miller%E2%80%93Rabin_primality_test#Python):

```python
def is_prime(n):

	# Credits: rosettacode.org, Miller-Rabin 
	# https://rosettacode.org/wiki/Miller%E2%80%93Rabin_primality_test

    if n!=int(n):
        return False
    n=int(n)

    if n==0 or n==1 or n==4 or n==6 or n==8 or n==9:
        return False
        
    if n==2 or n==3 or n==5 or n==7:
        return True
    s = 0
    d = n-1
    while d%2==0:
        d>>=1
        s+=1
    assert(2**s * d == n-1)
  
    def trial_composite(a):
        if pow(a, d, n) == 1:
            return False
        for i in range(s):
            if pow(a, 2**i * d, n) == n-1:
                return False
        return True  
 
    for i in range(8):
        a = random.randrange(2, n)
        if trial_composite(a):
            return False
 
    return True

def getNextPrime(number):

	while True:
		if is_prime(number):
			return number
		else:
			number += 1
```

Com isto obtivemos os valores de `p`, `q` e `n`. Com o expoente público conhecido, `e = 0x10001`, fomos em busca do valor de `d` que é o último necessário para descodificar a mensagem. Sabia-se que o número da procura apresentava a seguinte propriedade:

```note
(d * e) % ((p - 1) * (q - 1)) == 1
```

Em Python, a equação pode ser traduzida da seguinte forma:

```python
d = pow(e, -1, ((p-1)*(q-1)))
```

Com todos os valores descobertos, bastou chamar a função `dec(message)` disponibilizada pelo template:

```python
p = getNextPrime(2**512)
q = getNextPrime(2**513)
n = p*q
e = 0x10001
d = pow(e, -1, ((p-1)*(q-1)))

original_flag = dec(encoded_flag)
print(original_flag.decode())
```

![CTF 6 1](/img/ctf6task1.png)

O script com todo o código necessário para completar o CTF está disponível em [Semana11_CTF1](/CTF/Exploits/Semana11_CTF1.py).

## Segunda parte

Desta vez o servidor CTF na porta 6001 envia duas mensagens com o mesmo conteúdo (a flag) e cifradas com o mesmo valor de `n` mas diferentes `e`:

```note
message_1 = "e59b9d34740e0b93baa3fda0d9105e0152230ed2c2b24ee8bc0e3ffdf1a8d55895926ba23aa60547396dbf561b044c3d408800e9c80a286822353be4ad4876ae0cd3f3a
4a7c9d5039191696bd7abc66b2577736db86bf6a17d3867236215f01c13abf122054de5f4265decbcd366b125b826ca368b9e18bf91d9e688fd6503dbbc81ac8017fc083
5c3790a1cabf084b7044b5cf796c47b4946db80b750b8b00d7ab0b7b3cdd7eadce3062bb26eeaf7393d60422f1890543413d1e7f339fff50399ea2a9319aad8adffb749f
3cad52a5de42c629ccee564c2e7d815b399d237acf16545bf0c01c0f0bffa02d6afe402b2ce92f09f8ae05ba68dbf3794c406641b"

exponent_1 = 65537

message_2 = "8b072adb1b6b0eb67962da4f8c8bafa470c94500309613243048a7994f4a691ea2fec0b5886b192233b8f21e6f7fdb7d44ff19fa713e24e868960f0253ab697247f8c06
402517c6f534d6a9fd5ca9d1ce73b6dc3ce90a3e9d2b78644c5bbee0cd0e9c17e32ff27946ec41dcdebd0a694496c4ca93ef4a5f6f43782c72a159c583d121d7c909957b
e275b2a460ac3a2b36de775a58a4234099c0b71242d48bdaeaf7f5d447c7c18b93cfffa4ab74b4e59345d13bf746ddd639725dcb6de6d4130ef7a32152dc635679ee9365
956c7cf0457b10ae64d308a23bd76dbe09edf46f596ced7134d745f4fa365b8485e59d79909c172c1f20b2601ef55f172b81c667a"

exponent_2 = 65539

n = 2980238400733583611406079094694017226384968807420384720567916111924674096902469144725654375086484627396070843825431156628395262848442401
5493681621963467820718118574987867439608763479491101507201581057223558989005313208698460317488564288291082719699829753178633499407801126
4955897846002550760694676343648570187097454592889820609553726203121401340526855492032948287987004144655397432176095564520394669446649837
8134258755118567933464222297277087601964383590944613214645576415295817646501997007531906295237213483991255560375395925042434211558103197
9523075376134933803222122260987279941806379954414425556495125737356327411
```

Tal como o enunciado sugere, fomos em busca de ataques à criptografia RSA de acordo com as informações que possuíamos. Descobrimos o [Common Modulus Attack](https://infosecwriteups.com/rsa-attacks-common-modulus-7bdb34f331a5). Para implementar este ataque, primeiro é necessário traduzir as mensagens descobertas para valores inteiros:

```python
m1 = int.from_bytes(bytes.fromhex(message_1), "big")
m2 = int.from_bytes(bytes.fromhex(message_2), "big")
```

A mensagem original é descoberta seguindo a fórmula:

```note
decoded_message = ((pow(m1, a, n) * pow(m2, b, n)) % n)
```

Em que os valores de `a` e `b` seguem a seguinte propriedade:

```note
a*exponent_1 + b*exponent_2 == 1
```

Para descobrir os coeficientes `a` e `b` da equação anterior, recorremos ao *Extended Euclidean Algorithm*. O código usado foi extraído [desta fonte](https://www.techiedelight.com/extended-euclidean-algorithm-implementation/):

```python
def extended_gcd(a, b):

    # Credits: Techie Delight
    # https://www.techiedelight.com/extended-euclidean-algorithm-implementation/ 

    if a == 0:
        return b, 0, 1
    else:
        gcd, x, y = extended_gcd(b % a, a)
        return gcd, y - (b // a) * x, x

gcd, a, b = extended_gcd(exponent_1, exponent_2)

assert(gcd == 1)
assert(a*exponent_1 + b*exponent_2 == 1)
```

Com todos os valores encontrados descodificamos a mensagem:

```python
decoded_message = ((pow(m1, a, n) * pow(m2, b, n)) % n)
print(decoded_message.to_bytes(256, 'big').decode())
```

![CTF 6 2](/img/ctf6task2.png)

O script com todo o código necessário para completar o CTF está disponível em [Semana11_CTF2](/CTF/Exploits/Semana11_CTF2.py).