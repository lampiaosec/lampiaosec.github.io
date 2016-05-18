---
layout: post
title: Bypass de Privacidade por Domínio do Site do Vimeo
date: 2016-05-10 01:00:00
categories: articles
en: false
comments: true
description: Relato de como bypassar o acesso a vídeo do Vimeo privativo por site
keywords: bypass, hacking, vimeo, video, filme, privado
authors:
    - gjuniioor
    - userx
---

> **OBS:** Você pode ler esse post em inglês em [/articles/vimeo-privacy-bypass/](/articles/vimeo-privacy-bypass/)
>
> **NOTE:** You can read this post in english at [/articles/vimeo-privacy-bypass/](/articles/vimeo-privacy-bypass/)

# Índice
{:.no_toc}

* toc
{:toc}

# Introdução

Na [#ViradaHacker][virada-hacker] do dia primeiro de abril foi [relatada uma forma][relato-do-bypass] [^0] de como poder assistir a um vídeo privado no Vimeo. Nossa abordagem foi bem rápida sobre o assunto, porém pela página no [facebook do LampiãoSec][facebook-do-lampiao], e-mail e telegram, recebemos pedidos e incentivo para fazer um post mais detalhado, que mostrasse melhor a teoria por trás do processo. Atendendo a esse pessoal e a nossa vontade de ajudar como pudermos a comunidade, demorou mas saiu.

## Motivação
{:id="motivacao"}

Mostrar e fazer entender como essa solução foi pensada é algo importante, tanto para quem está iniciando na área, pois assim ele passa a ver como o processo foi pensado e tudo mais, quanto para a própria comunidade, pois é menos um how-to criando os *hackers de facebook* por aí.

## O Problema

O que acontecia era o seguinte: precisávamos que um amigo assistisse a um vídeo que estava hospedado no Vimeo mas esse vídeo estava configurado para ser exibido apenas nesse determinado site, e o amigo não tinha acesso a ele. Então, foi pensado o seguinte: se o Vimeo deixa apenas tal site exibir, vamos dizer que minha máquina é esse site, e tentar fazer isso. Após os testes feitos, tudo funcionou corretamente, e você pode ver esse relato, originalmente, no blog do #ViradaHacker [^0].

# Teoria

Basicamente, quando falamos de web, falamos do HTTP (Hipertext Transfer Protocol). Portanto, para entender melhor o procedimento, é bom que se tenha um entendimento, nem que seja básico, do funcionamento desse protocolo. Caso precise de um reforço, indicamos o e-book gratuito HTTP Succinctly [^1], pois uma explicação sobre o HTTP já não faz parte do escopo desse post.

Mas vamos relembrar algumas coisas para manter o foco: lembre-se que o HTTP é um protocolo em texto, então toda sua manipulação por nossa parte é feita muito simplesmente. Lembre-se também dos cabeçalhos HTTP e seus parâmetros. O mais importante deles para o escopo de agora é o campo **Referer** [^2]. Esse campo informa ao servidor de onde o usuário conseguiu e referência (nome suspeito né?!) para acessar determinado recurso. Ou seja, se você clicar em algum link nessa página, o servidor, que estiver hospedando a página para onde você foi, saberá que veio daqui. Portanto, se você acessar algum link que viu aqui, mas digitando seu endereço, o servidor não terá essa informação.

E é justamente por esse campo que o Vimeo faz a verificação. Veja exemplo:

![Cabeçalho HTTP de requisição ao Vimeo]({{ site.baseurl }}/assets/img/posts/vimeo/01.png){:class="imghalf"}

E essa é toda a teoria que precisamos saber para entender esse hack. Vamos à prática!

# Prática
{:id="pratica"}

Sabendo que é por ai que o Vimeo faz a verificação se o domínio informado no campo Referer [^2] bate com o informado pelo usuário nas configurações do vídeo, podemos então manipular a requisição, informando nesse campo o que o Vimeo quer saber. Vamos precisar apenas editar as requisições HTTP para o `player.vimeo.com`. Podemos fazer isso de várias maneiras: Web proxy, criando nossa própria requisição ou gerando uma da forma que precisamos. Vamos mostrar algumas formas, mas não fique limitado à essas. Inclusive, se estiver um outro modo que queira compartilhar, esteja convidado a fazer nos comentários ou entre em contato conosco para adicionar sua contribuição aqui.

## Mudando DNS

O primeiro método a ser mostrado será o originalmente testado, e que já foi mostrado no post [^0], lá no [#ViradaHacker][virada-hacker]. Esse consiste em criarmos um servidor local, atribuir o domínio que desejamos e pronto.

### Primeiro Passo: Criar servidor

Primeiramente, precisamos criar nosso servidor. Podemos fazer isso de diversas maneiras, mas a escolhida aqui foi com um módulo python, já que este está presente na maioria das distribuições GNU/Linux por padrão. Então, basta executar o comando abaixo, modificando alguns dados:

~~~
$ sudo python -m http.server --bind seu_ip 80
~~~

* Mude o `seu_ip` justamente para, veja só, o IP de sua máquina. Um exemplo de como conseguir é com o comando `ip addr`;
* O `sudo` foi necessário pois utilizamos a porta 80 para facilitar o processo.

### Segundo Passo: Criar página com o vídeo

Nesse mesmo diretório que rodou o servidor, crie uma página `index.html` (ou qualquer outra, claro :] ) com um widget do vídeo. Para facilitar o seu trabalho, pode copiar e colar se quiser:

~~~
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Bypass de Privacidade do Vimeo :: LampiãoSec</title>
</head>
<body>
	<iframe style="width: 100%; height: 500px;" src="https://player.vimeo.com/video/109727604" frameborder="0" allowfullscreen></iframe>
</body>
</html>
~~~

> **OBS:** O vídeo ai pertence à [Cybrary]. Estamos fazendo com ele como exemplo pois é um projeto bacana e de livre acesso, portanto, não há prejudicados na história. :)

Se você fizer a requisição agora, poderá notar que não consegue ver o vídeo, pois está bloqueado. E se analisar o cabeçalho da comunicação, poderá notar que foi retornado um 403, justamente porquê o **referer** está diferente do permitido.

![Requisição sem mostrar o vídeo]({{ site.baseurl }}/assets/img/posts/vimeo/02.png){:class="imghalf"}

### Terceiro Passo: Informar referer necessário

Agora precisamos mostrar para o Vimeo que somos a Cybrary. Como podemos fazer isso? Com esse método pensamos em mudar o endereço de resposta da Cybrary, para quando acessarmos `cybrary.it` no browser, seja retornado a página que queremos.

Para isso, mudamos a resposta do DNS, informando que agora nosso IP responde por `cybrary.it`. Poderíamos fazer isso montando um servidor DNS fazendo um envenenamento do Cache, mas com o intuito de facilitar, vamos apenas alterar o `/etc/hosts` (o que na verdade não deixa de fugir das outras possibilidades). Para isso, insira a seguinte entrada nesse arquivo:

~~~
seu_ip	dominio_permitido	dominio_permitido
~~~

> Claro, alterando as partes variáveis, como `seu_ip` e `dominio_permitido`.

Feito! Agora se fizermos a requisição novamente podemos ver que foi retornado sucesso, e olhando lá para o **referer**, notamos a mudança:

![Requisição mostrando o vídeo]({{ site.baseurl }}/assets/img/posts/vimeo/03.png){:class="imghalf"}

# Outros Métodos
{:id="outros-metodos"}

Mostrado e explicado o modo pelo qual chegamos ao resultado, vamos fazer isso de outras maneiras, com outras ferramentas, para que não fiquemos presos àquela ideia da simples receita de bolo. Portanto, priorizamos mais aqui que você entenda a técnica que apenas execute-a, ok?!

## Alterando a requisição HTTP com BurpSuite

Vamos mostrar aqui, agora, como fazer isso através da alteração do cabeçalho da requisição HTTP. Para isso, primeiro precisamos abrir o Burp e configurar nosso navegador. Feito esses passos, vamos fazer uma requisição para o `player.vimeo.com`, informando o ID do vídeo. Portanto, o endereço será:

~~~
https://player.vimeo.com/video/109727604
~~~

Logo na primeira requisição que aparecer vamos inserir nossa alteração:

~~~
Referer: https://cybrary.it
~~~

Lembre-se de que só colocamos `https://cybrary.it` pois é o domínio que o vídeo está configurado, adapte à sua necessidade.

![Alteração do cabeçalho da requisição HTTP com BurpSuite]({{ site.baseurl }}/assets/img/posts/vimeo/04.png){:class="imghalf"}

## Fazendo a requisição com cURL & wget

Em todos os momentos precisamos interagir diretamente com vários programas e fazer a alteração, basicamente, a cada momento que quiséssemos ver ao vídeo. Após a página do vídeo ser carregada nós podemos fazer o download dela e não precisar mais nos preocuparmos com sempre alterar tudo. Com o `curl` ou `wget` isso fica muito mais simples. Vamos ver:

* cURL

~~~
$ curl --referer https://cybrary.it -o curl.html https://player.vimeo.com/video/109727604
~~~

* wget

~~~
$ wget --referer https://cybrary.it -O wget.html https://player.vimeo.com/video/109727604
~~~

Perceba que o que tem de mudança entre um comando e outro é o parâmetro para o nome do arquivo salvo. Ambos alteram o `referer` da mesma maneira. Isso facilita bastante caso queira fazer algum script para algo. :)

## Criando a requisição com ncat

Agora, vamos praticar o que foi visto, criando a requisição com o ncat, invés de usar algum programa para fazer isso.

*- Mas por quê ncat e não netcat?* - perguntou ele.

*- Pois precisamos de SSL nessa requisição* - respondemos nós (rs')

Vamos lá, é coisa simples:

~~~
$ ncat --ssl player.vimeo.com 443 | grep -i "doctype" > ncat.html
	GET /video/109727604 HTTP/1.1
	Referer: https://cybrary.it
	Host: player.vimeo.com
	User-Agent: LampiãoSec
	Connection: close
~~~

**OBS:** Usamos um TAB na frente de algumas linhas indicando que elas serão escritas dentro do `ncat` ;)

Frente a tudo que foi explicado, acreditamos que essa requisição é entendível. Mas caso fique alguma dúvida, sinta-se livre para entrar em contato conosco.

# Conclusão

Vamos ser sucinto agora: **Descubra até onde vai a toca do coelho**.

# Referências
{:id="referencias"}

[^0]: [Relato do bypass](https://lampiaosec.github.io/virada-hacker/2016/04/02/diario-de-bordo/#ver-vdeo-privado-do-vimeo-localmente)
[^1]: [Review do HTTP Succinctly](https://gjuniioor.github.io/books/http-succinctly/)
[^2]: [Campo Referer](https://tools.ietf.org/html/rfc2616#section-14.36)

[virada-hacker]: https://lampiaosec.github.io/virada-hacker/
[relato-do-bypass]: https://lampiaosec.github.io/virada-hacker/2016/04/02/diario-de-bordo/#ver-vdeo-privado-do-vimeo-localmente
[facebook-do-lampiao]: https://facebook.com/lampiaosec
[cybrary]: https://cybrary.it
