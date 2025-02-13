# Trickster
###### Solved by @beatriz-a-cardozo & @CupNudous
> Esse é um CTF que envolve Web Exploitation e Pentest
## About the Challenge
Na descrição do desafio, temos o texto 'Encontrei um aplicativo web que pode ajudar a processar imagens: imagens PNG apenas! Detalhes adicionais ficarão disponíveis depois de lançar a instância do seu desafio'.

Então, após fazermos o que o enunciado pede e lançar a instância, encontramos um site simples que tem a opção de escolhermos um arquivo e dar upload nele.

## Solution
O site que encontramos quando abrimos o link após gerar a instância é bem objetivo, tem apenas o título, o botão de selecionar arquivo e o botão de upload. 

[![imagem-2025-02-13-164822137.png](https://i.postimg.cc/NGXzZGPX/imagem-2025-02-13-164822137.png)](https://postimg.cc/NLgDHttG)

De primeira, tentamos colocar uma imagem png qualquer, para ver se o site nos diz onde ela fica armazenada, mas ele apenas devolve que o arquivo foi enviado com sucesso e é um png válido. Agora tentando com um arquivo que não é png, vemos que ele não é aceito e retorna um erro.

Atrás de mais informações, tentamos usar o url 'robots.txt'

> http://atlas.picoctf.net:49402/robots.txt

Lá eles nos dão dois endereços, o primeiro **/instructions.txt** que temos as informações da validação de PNG que o site usa: deve ter a extensão '.png' e os magic bytes de um png (hexadecimal com 50 4E 47), o outro **/uploads/**, que quando tentamos acessar estamos bloqueados (Forbidden).

Com isso, podemos traçar um plano de criar um payload que se passe por um png e que nos permita executar comandos dentro do site para procurarmos algo que possa nos remeter a flag.

Primeiro, para conseguir executar um código no site percebemos que iriamos precisar de um código php, como nós não temos conhecimento nessa linguagem, procuramos por um código pronto e encontramos no github o 'easy-simple-php-webshell.php' do @joswr1ght, o link para ele é https://gist.github.com/joswr1ght/22f40787de19d80d110b37fb79ac3985. Com isso, só precisamos adicionar os magic bytes no começo do código para que ele se passe por um png.

De acordo com a wikipedia (que é onde o instructions.txt nos diz que iriamos encontrar o magic byte), vemos que o hexadecimal de identificação de um png é '89 50 4E 47 0D 0A 1A 0A'.

Aqui está como ficou nosso payload completo, para funcionar é necessário que o arquivo seja nomeado com **< qualquer_coisa >.png.php**, pois ele válida se tem a extensão .png, e precisicamos que seja um .php para executa-lo:

[![imagem-2025-02-13-175052654.png](https://i.postimg.cc/XNHd49GB/imagem-2025-02-13-175052654.png)](https://postimg.cc/dDCL9k9J)

Inicialmente, colocamos apenas o hexadecimal, mas ele nos retornou um erro de que não é um png válido, então colocamos 'PNG' escrito no começo do código, e assim foi dado upload com sucesso.

Agora, vamos para o endereço que conseguimos no /robots.txt e vemos se o nosso arquivo está lá, então pesquisamos por:

> http://atlas.picoctf.net:54721/uploads/<nome_do_seu_arquivo>.png.php

Graças ao código open source que pegamos do GitHub temos uma interface de forms para executar os comandos que desejamos. Começamos testando o comando 'ls', mas nos retornou apenas o nome do nosso arquivo, isso está correto já que estamos no diretório dele. Então resolvemos usar um comando para buscarmos em todo o site por qualquer arquivo .txt: **find :/name '.txt'**.
[![Captura-de-tela-2025-02-13-173744.png](https://i.postimg.cc/x1rk1508/Captura-de-tela-2025-02-13-173744.png)](https://postimg.cc/ppZV0D5M)

Desses aqui, o mais suspeito é um com o nome com letras maiusculas aleatórias: MQZWCYZWGI2WE.txt. Para verificarmos, usamos um **cat ../MQZWCYZWGI2WE.txt** e isso nos dá a flag.

> picoCTF{c3rt!fi3d_Xp3rt_tr1ckst3r_d3ac625b}
