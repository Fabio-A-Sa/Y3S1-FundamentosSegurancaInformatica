# CTF 5 - Semana 10

## Primeira parte

Inicialmente exploramos o site disponibilizado na plataforma CTF na porta 5002. <br>
Submetemos uma justificação só para testar o funcionamento do sistema e obtivemos assim o acesso a dois botões desativados, sendo que um deles contêm o texto "Give the flag".

![Aspecto inicial do site](../img/ctf5task1a.png)

O html de esse botão é o seguinte:

```html
<form method="POST" action="" role="form">
    <div class="submit">  
        <input type="submit" id="giveflag" value="Give the flag" disabled="">  
    </div>
</form>
```

Com a vulnerabilidade XSS forçamos a ativação do botão inserindo o seguinte código no campo de texto:

```html
<script>document.getElementById('giveflag').click()</script> 
```

Após o refresh automático da página, a flag é visualizada:

![XSS Attack 1](../img/ctf5task1b.png)

## Segunda parte

Inicialmente exploramos o site disponibilizado na plataforma CTF na porta 5000. <br>
Um utilizador sem estar autenticado consegue ter acesso à funcionalidade "Ping a host", o que no contexto do CTF faz sentido. A funcionalidade "ping" está diretamente ligada a utilitários Linux e por isso o input deverá ser corrido num terminal. Testamos então introduzir o seguinte input de modo a realizar um *command injection*:

```note
;ls
```

E funcionou, visto que obtivemos o seguinte resultado:

![Ping](../img/ctf5task2a.png)

Assim, tentamos aceder ao ficheiro da flag com o seguinte input:
```note
;cat /flags/flag.txt
```

E verificamos que resultou:

![XSS Attack 2](../img/ctf5task2b.png)
