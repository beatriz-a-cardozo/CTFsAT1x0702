# Web Gauntlet 1
###### Solved by @beatriz-a-cardozo, @CupNudous
> Esse é um CTF que envolve Web Exploitation e Pentest, mais especificamente SQL Injection.

## About the Challenge

Essa é uma questão do picoCTF que explora vulnerabilidades web e SQL Injection simples, ela fornece 2 links, um contendo um sistema de login simples, que pede apenas usuário e senha, com um marcador de 1/5 "rodadas", o outro link te leva para uma página que diz os filtros de caracteres contidos naquela "rodada", sendo que a cada rodada passada, o filtro passa a reter mais coisas, dificultando a injeção dos comandos, o objetivo final é logar como admin 5 vezes seguidas.

## Solution

Essa questão do picoCTF envolve a exploração de SQL Injection, onde o objetivo é realizar login como admin por cinco rodadas seguidas, enfrentando filtros cada vez mais restritivos.

>Round 1:

Inserindo algo qualquer no sistema de login e tentando logar como "a" em ambos login e senha, o sistema te retorna no topo da página a consulta SQL que é feita no banco de dados, como na imagem a seguir:
 [![imagem-2025-02-12-185612716.png](https://i.postimg.cc/43hbwDXS/imagem-2025-02-12-185612716.png)](https://postimg.cc/McqQprM7)
 
>`SELECT * FROM users WHERE username='a' AND password='a'`

Isso indica que podemos manipular essa consulta utilizando SQL Injection. Ao acessar o segundo link que mostra os filtros aplicados, vemos que nesta primeira rodada apenas o operador `OR` está sendo filtrado. Isso significa que podemos utilizar um comentário simples (--) para ignorar a parte da consulta relacionada à senha e burlar o login.

Payload utilizado: `admin' --`

>Round 2:

Nesta rodada, a consulta realizada no banco de dados se mantém a mesma, porém, checando o link com fim "filter.php", percebe-se que os filtros foram ampliados para bloquear os operadores `OR`, `AND`, `LIKE` e o comentário simples `--`. Isso impede o uso do comentário para ignorar a verificação de senha. No entanto, o operador `/* */` para comentários de múltiplas linhas ainda está disponível, ignorando a verificação de senha novamente.

Payload utilizado: `admin' /*`

>Round 3:

Após passar pela 2ª rodada com o payload acima e checando o segundo link fornecido novamente, vemos que o filtro adicionou `>`, `<` nos caracteres retidos, dificultando ainda mais a injeção. A solução então foi utilizar o caractere ";", visto que o ponto e vírgula encerra a consulta original do ponto que ele é colocado, permitindo autenticação sem a necessidade de uma senha.

Payload utilizado: `admin';`

>Rounds 4 e 5:

Tendo passado então a terceira rodada e checando os filtros da quarta e da quinta rodadas, entende-se que agora a palavra "admin" por si só é filtrada, mas nenhum operador novo como o `;` é barrado, portanto, a solução mais simples seria manter o payload `admin';` usado no último nível, porém, adicionando o operador `"||"`, usando da concatenação em SQL para reconstruir a string `admin` na consulta realizada, assim, permitindo a autenticação.

Payload utilizado: `ad'||'min';`

>Flag:

Checando então o link com fim `"filter.php"`, na última linha se localiza a flag desse desafio:
[![imagem-2025-02-13-153348940.png](https://i.postimg.cc/8zfhHyTk/imagem-2025-02-13-153348940.png)](https://postimg.cc/FYmdFVfw)

>`picoCTF{y0u_m4d3_1t_16f769e719ab9d3e310fd13dc1262ee1}`
