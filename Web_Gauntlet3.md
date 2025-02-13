# Web Gauntlet 3
###### Solved by @beatriz-a-cardozo, @CupNudous
> Este é um CTF sobre Web Exploitation, Pentest e SQL Injection

## About the Challenge

Esse desafio proposto pelo PicoCTF contém dois links,um deles uma tela de login simples, que requisita apenas usuário e senha, e o outro nos dá uma lista de filtros que bloqueiam alguns caracteres específicos que podem ser usados para realizar ataques a bancos de dados SQL. Desse modo, os operadores `OR, AND, TRUE, FALSE, UNION, LIKE, =, >, <, ;, --, /*, */` e a palavra `admin` são retidos pelo filtro de segurança do sistema

## Solution

Começamos então testando o sistema de login e suas restrições.

Inserindo algo qualquer no sistema de login, tentando logar como "a" em ambos login e senha, o sistema retorna no topo da página a consulta SQL que é feita no banco de dados, como na imagem a seguir:
[![imagem-2025-02-13-161310625.png](https://i.postimg.cc/26KGFYGH/imagem-2025-02-13-161310625.png)](https://postimg.cc/CBbbSWWD)
 
>`SELECT username, password FROM users WHERE username='a' AND password='a'`

Sendo assim, conhecendo os filtros aplicados devido ao segundo link fornecido, e a consulta, podemos tentar manipular essa consulta utilizando SQL Injection.

Sabemos que a palavra `"admin"` está sendo filtrada, no entanto, nada nos impede de concatenar a palavra, para que a string se junte quando a consulta for feita, usando o seguinte payload:

>`ad'||'min`

As duas barras servem para juntar duas ou mais strings. Graças a isso, podemos escrever 'admin' de uma maneira que não seja filtrada. A falta de aspas no começo e no fim se deve ao fato de que o próprio banco de dados já coloca aspas antes e depois. No entanto, isso não completa o desafio, pois ainda não somos capazes de passar pela senha, já que as opções usadas para comentar e ignorar a sessão da senha foram barradas, fazendo bypass apenas do login.

De certo modo, esse exercício poderia ser resolvido usando a função `"GLOB"`, uma função muito similar ao `"LIKE"`, que retorna o resultado da busca e aceitam caracteres curingas como `"*"`, que significa 'all'. Usando o payload `' glob '*`, conseguiríamos realizar esse desafio e alcançar a flag perfeitamente, porém, estaríamos usando a mesma resolução do `Web Gauntlet 2`, então decidimos tentar de outra maneira.

Usando então o operador de comparação `IS NOT`, conseguimos burlar a consulta de senha da mesma forma, usando uma condição verdadeira como `a' IS NOT 'b`, uma comparação que retornar verdadeiro, então o payload final ficaria:

>username: `ad'||'min`

>password: `a' IS NOT 'b`

Acessando como admin, completamos o desafio, conseguindo a flag:
[![imagem-2025-02-13-165908215.png](https://i.postimg.cc/MGHc8dvh/imagem-2025-02-13-165908215.png)](https://postimg.cc/Y4c26NN3)


>`picoCTF{k3ep_1t_sh0rt_ef4a5b40aa736f5016b4554fecb568d0}`