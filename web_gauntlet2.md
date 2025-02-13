# Web Gauntlet 2
###### Solved by @beatriz-a-cardozo & @CupNudous
> Esse é um CTF que envolve Web Exploitation e Pentest, mais especificamente SQL Injection.

## About the Challenge

O desafio é proposto pelo PicoCTF e contém dois links: um com uma tela de login simples, que requisita apenas username e senha, e, de acordo com o enunciado, temos que logar como 'admin' para completarmos o desafio. O outro link nos dá uma lista de filtros que bloqueiam certas palavras que podem realizar funções maliciosas em um banco de dados. Os filtros do Web Gauntlet 2 são: 'or', 'and', 'true', 'false', 'union', 'like', '=', '>', '<', ';', '--', '/*', '*/', 'admin'.

## Solution

Primeiro, verificamos se a query ainda é a mesma do primeiro exercício, inserindo qualquer coisa (que não seja filtrada) nas duas caixas de resposta.

> SELECT username, password FROM users WHERE username='a' AND password='a'

Continua a mesma.

Já de primeira, vemos um problema que encontramos na última instância da Web Gauntlet 1: a palavra 'admin' está sendo filtrada, então, usando a mesma solução de antes, podemos concatenar a palavra assim:

> ad'||'min

As duas barras servem para juntar duas ou mais strings. Graças a isso, podemos escrever 'admin' de uma maneira que não seja filtrada. A falta de aspas no começo e no fim se deve ao fato de que o próprio banco de dados já coloca aspas antes e depois. No entanto, isso não completa o desafio, pois ainda não somos capazes de passar pela senha, apenas conseguimos o login.

O site não tem nenhuma outra seção para explorar e encontrar a senha, e bloqueia todas as formas de quebra de linha ou comentário (do SQLite). Além disso, temos outros filtros para algumas funções que seriam úteis para conseguir a senha, principalmente a função 'like', que serve para retornar palavras ou padrões que desejamos.

É aí que está a resolução para o problema. Apesar de filtrarem 'like', existe outra palavra que tem uma função bem parecida: **glob**. Essas duas fazem a mesma coisa: retornam o resultado da busca e aceitam caracteres curingas como '*', que significa 'all'. A diferença entre elas é que **glob** é case-sensitive, assemelhando-se mais a uma expressão regular, sendo mais rígida.

Então, sabendo disso, podemos inserir no site:

**username:**
> ad'||'min

**password:**
> ' glob '*

No campo de senha, fechamos a parte da senha com uma aspas simples e usamos o **GLOB** para retornar tudo '*' (não fechamos as aspas porque elas serão fechadas pela query do site).

E isso nos garante o acesso à conta admin, completando o desafio e nos dando a flag:

> picoCTF{0n3_m0r3_t1m3_838ec9084e6e0a65e4632329e7abc585}
