---
title: "O HTTP e o Express Js"
date: 2018-05-04T11:27:25-03:00
draft: false
---

O Express é um micro-framework criado para quem quer desenvolver aplicações web usando Node.js. Uma de suas principais responsabilidades é a
de abstrair os conceitos de requests e responses HTTP, permitindo a criação de mecanismos que empoderam a aplicação para receber requests,
processá-los e enviar uma resposta (response) http de volta a o cliente (programa que enviou o request).

Antes de entendermos o que é o Express ou até mesmo como criar uma aplicação web em Node sem o Express, precisamos entender alguns conceitos
básicos sobre como a internet funciona.

## Como a internet funciona?

De uma maneira extremamente simplificada, podemos entender que a internet nada mais é do que uma rede de computadores conectados,
conversando entre si.

Para que essas conversas ocorram, é necessário que os computadores tenham linguagens formais para que todo mundo possa se entender. Imagine
alguém que somente fala italiano tentando falar com alguém que fala somente espanhol, seria impossível estabelecer uma comunicação
eficiente.

_Tais linguagens são conhecidas como_ _**protocolos**_.

Cada **protocolo** é utilizado para um tipo específico de conversa, por exemplo:

* **SMTP:** é um dos protocolos utilizados para a troca de emails
* **FTP:** é utilizado para trocar arquivos entre computadores
* **HTTP:** utilizado para a transferência de conteúdo hipertexto

### O HTTP

O protocolo HTTP pode ser entendido como **a fundação da internet**, pois ele permite que se envie informações de arquivos HTML, CSS e
JavaScript de um computador para o outro, o que é fundamental para a vida das páginas web \(a forma principal de comunicação utilizada na
internet\).

**Isso quer dizer que, quando alguém acessa um site, para poder vê-lo funcionando num browser é preciso antes receber os dados contendo as
definições HTML, CSS e JavaScript deste site através do HTTP.**

Evidentemente, não basta apenas receber tais dados, eles precisam ser interpretados para que façam sentido e se tornem uma página bonita e
funcional. Essa é mais uma tarefa para os browsers: interpretar os dados recebidos por HTTP e transformá-los em páginas utilizáveis por
seres humanos.

### Browser e HTTP passo a passo

Então, observando o que acontece quando uma pessoa acessa um site qualquer, podemos ver tudo que um browser faz por nós, humanos:

**Comunicação HTTP:**

* Alguém acessa o site no endereço _www.google.com.br_
* O browser faz uma **requisição HTTP** para o servidor que está no endereço _www.google.com.br_
  * Em outras palavras, o browser diz, falando em HTTP com o servidor: _"Por favor, me passe os dados da página"_
* O servidor, de maneira formal, responde ao browser com uma **resposta HTTP** contendo as informações pedidas de forma organizada e
  padronizada, de um jeito que browser consegue entender o que foi recebido

**Renderização de uma página:**

* O browser recebe o conteúdo HTML, CSS e JS que foi enviado pelo servidor através do HTTP
* O browser começa a interpretar tal conteúdo, transformando-o em uma página
  * Baseada na estrutura descrita pelo HTML
  * Nos estilos descritos nos CSS
  * E em tudo mais que estiver nos JavaScripts

### Como se parece uma requisição HTTP?

No exemplo anterior, o browser fez uma requisição para o site **www.google.com.br**. Na prática, ele enviou através de uma comunicação
HTTP, um texto mais ou menos como este:

```http
GET  HTTP/1.1
Host: www.google.com.br
Cache-Control: no-cache
```

E o servidor da Google, por sua vez, respondeu com algo mais ou menos assim:

```html
<!doctype html><html itemscope="" itemtype="http://schema.org/WebPage" lang="pt">
  <head>
  <meta content="text/html; charset=UTF-8" http-equiv="Content-Type">
  <meta content="/images/branding/googleg/1x/googleg_standard_color_128dp.png" itemprop="image">
  <title>Google</title>
  <script>
    (function(){
      window.google={kEI:'2vToWbWNOcObwQSXhKLADA',kEXPI:'1352821,1353383,1354277,1354401,1354915,1355159,1355218,1355325,1355527,1355735,1355800,1355820,3700263,3700440,3700476,4029815,4031109,4043492,4045841,4048347,4072774,4076999,4078430,4081039,4081164,4092182,4093169,4095909,4097153,4097470,4097922,4097929,4098721,4098728,4098752,4102238,4103475,4103861,4104258,4104414,4108824,4109316,4109489,4110656,4110685,4111016,4113215,4114597,4115289,4116926,4116935,4117328,4117980,4118226,4118437,4118798,4119272,4119740,4120415,4120660,4121035,4121518,4122185,4122352,4124091,4124174,4124411,4124497,4124850,4124893,4124968,4124975,4125837,4125873,4125962,4126204,4127473,4127555,4127657,4127744,4127775,4127776,4128586,4128874,4129520,4129555,4129633,4130575,4130783,4131247,4131286,4131834,4132255,4132566,4132784,4132953,4133090,4133113,4133245,4133416,4133424,4134266,4134327,4134560,4135088,4135300,4135576,4135954,4135968,4136205,4136223,4136399,4136549,4137482,4137548,10200083,10200096,10201956,16200027,19003868,19003881,19003883,19003900,19003901,19003907,19003909,19003910,19003913,19003927',authuser:0,kscs:'c9c918f0_2vToWbWNOcObwQSXhKLADA',u:'c9c918f0',kGL:'BR'};google.kHL='pt';
    })();

...
```

Ou seja, a resposta do browser foi um documento HTML, contendo scripts JavaScript e links para outros documentos CSS e mais scripts. Esta é
a informação que o browser interpretará e transformará na famosa página de busca da Google.

### HTTP na prática

Caso você queira ver essa comunicação acontecendo na prática, pode usar o comando `telnet`:

Primeiro, estabeleça a conexão com a Google via telnet:

```sh
telnet google.com 80
```

Após a execução deste comando, a seguinte mensagem deve aparecer:

```
Trying 172.217.4.46...
Connected to google.com.
Escape character is '^]'.
```

Após esta mensagem, digite o seguinte e pressione `enter`:

```sh
GET /
```

Depois disso, o servidor da Google irá responder com um monte de HTML (que é o que o nosso browser recebe quando navegamos até google.com) e
então encerrará a conexão.

Neste último comando, existem duas informações muito importantes à respeito de como se organiza uma aplicação web:

- O verbo HTTP (no caso do comando, usamos o `GET`)
- A rota (no caso, usamos `/`)

```sh
GET   /
^^^   ^
Verbo Rota
```

## Verbos e rotas HTTP

O protocolo HTTP permite especificar um __verbo__ e uma __rota__ quando fazemos uma requisição para uma aplicação web. Estas duas
informaçoes nos ajudam a organizar a aplicação, separando a lógica que deve ser implementada para cada rota e verbo.

### Verbos

O HTTP possui vários verbos que são utilizados para descrever qual o tipo de operação queremos executar. Os mais relevantes geralmente são:

- __GET__ : Quando indicamos que o request que estamos fazendo quer buscar informações da aplicação
- __POST__: Usamos post para enviar informações para que a aplicação as salve em algum lugar (um banco de dados, por exemplo)

### Rotas

As rotas não possuem um padrão definido (como os verbos). Fica a critério de quem está desenvolvendo a aplicação definir quais rotas ela
responderá. Quando algum programa faz um request para uma rota que não é suportada pela aplicação, temos o famoso erro `404`.

### Um exemplo mais prático de rotas e verbos

Para entender melhor quando e como utilizar determinados verbos e rotas, vamos pensar em um exemplo: Um _e-commerce_.

Primeiro, vamos pensar em algumas funcionalidades comuns de um site de e-commerce:

- Lista de produtos
- Login
- Carrinho
- Busca

Agora, imaginando que estamos criando a aplicação de e-commerce, como suportaríamos estas funcionalidades? Vamos pensar somente na parte do
HTTP, sem mais detalhes.

__O site__

Primeiro, por se tratar de um site, vamos assumir que nossa aplicação estará hospedada no endereço `www.o-comercio.com.br`.

__A Lista de produtos__

Para implementarmos uma lista de produtos, é bem provável que usaremos o método http `GET`, já que queremos buscar uma lista de produtos e
exibí-la para as pessoas usuárias. Portanto, podemos ter nossa primeira rota: `/produtos`.

Um exemplo de pseudo-request seria:

```
GET www.o-comercio.com.br/produtos
```

__Login__

Para que uma pessoa possa fazer login no site, ela precisa fornecer um nome de usuário e senha. Já que aplicação precisa receber informações
para executar a tarefa de login, esta rota precisa ser do tipo `POST`. Podemos chamá-la de `/login`.

Um exemplo de pseudo-request seria:

```
POST www.o-comercio.com.br/login
usuario=doot&senha=tood
```

__Carrinho__

Um carrinho ficará atrelado à um perfil de usuária, então cada vez que a pessoa adicionar um novo item no carrinho, ela terá que fornecer
para a aplicação o produto que foi adicionado e seu nome de usuário, para que a aplicação saiba para quem atribuir aquele produto. Podemos
chamar nossa rota de `/adicionar-produto`. Como precisamos fornecer informações, esta rota terá que ser do tipo `POST`.

Um exemplo de pseudo-request seria:

```
POST www.o-comercio.com.br/adicionar-produto
usuario=doot&idProduto=123123
```

__Busca__

Nossa última funcionalidade é a de busca. Onde a pessoa digita o nome de um produto e a aplicação exibe uma lista de produtos que tenham um
nome parecido com o que foi digitado. Nesta rota, também temos que enviar dados para a aplicação (o nome do produto pesquisado), no entanto,
_estes dados não ficarão guardados na aplicação_, eles apenas serão usados para efetuar a pesquisa, logo, esta rota pode ser do tipo `GET`,
ainda que ela envie dados para o servidor. Podemos chamar esta rota de `/busca`.

Um exemplo de pseudo-request seria:

```
GET www.o-comercio.com.br/busca?nomeProduto=balde
```

Pode-se observar que esta URL possui detalhes a mais, mais especificamente, este pedaço: `?nomeProduto=balde`. Chamamos esta parte da URL de
[query](https://en.wikipedia.org/wiki/Query_string) e ela serve para passarmos parâmetros para um request `GET`. Em um request do tipo
`POST`, os dados enviados ficam no corpo da requisição. Usamos as `queries` em requests `GET` porque requests do tipo `GET` _não possuem
corpo_. Não se preocupe se isso ficar abstrato demais agora, uma vez que vejamos código, isso ficará mais claro.

Basicamente, é assim que nos comunicamos com uma aplicação via HTTP. Vamos ver como implementar isso com código.

## A lista de produtos com Node.js (sem Express)

Vamos ver como implementaríamos a lista de produtos da aplicação do `o-comercio` usando nada além de Node.js. Para fins de simplicidade,
vamos assumir o seguinte:

- A aplicação não se conecta com banco dados, toda a informação ficará na memória
- Vamos executar a aplicação na nossa própria máquina (o que conhecemos por [localhost](https://en.wikipedia.org/wiki/Localhost))

Vamos começar:

Antes de mais nada, temos que criar o arquivo que vai conter o código da nossa aplicação. Vamos fazer o seguinte:

```sh
# Criamos uma pasta para o projeto
mkdir o-comercio

# Acessamos a pasta
cd o-comercio

# Criamos o arquivo que conterá o código
touch server.js
```

Agora, escrevemos o código que irá receber `requests` e enviar `responses` HTTP:

```javascript
const http = require('http')

// Nossa lista de produtos
const produtos = [
  'Balde',
  'Vassoura',
  'Copo',
  'Cavalo',
  'Relogio'
]

const server = http.createServer((request, response) => {

  // Se o request for na rota produtos e for do tipo get...
  if (request.method === 'GET' && request.url === '/produtos') {
    response.write('Lista de produtos: \n')
    response.write(produtos.join('\n'))
    return response.end()
  }

  // Senao, erro 404...
  response.write('Rota não encontrada')
  return response.end()
})

// Vamos esperar requests na porta 3000
server.listen(3000, () => console.log('A aplicacao iniciou, visit http://localhost:3000/produtos'))
```

Ao executarmos esse script, podemos acessar o endereço no navegador e então teremos algo como:

![Uma lista de produtos no navegador](/images/2018/05/04/lista-de-produtos.png)

## Implementando a busca de produtos

E se quisermos adicionar a funcionalidade de busca? Temos que alterar nosso código:


```javascript
const http = require('http')
const url = require('url')

const produtos = [
  'Balde',
  'Vassoura',
  'Copo',
  'Cavalo',
  'Relogio'
]

const buscaProdutos = (produtoBuscado) => produtos.filter(produto => produtoBuscado === produto)

const pescaNomeDoProduto = (rota) => {
  nomeProduto = rota.query.split('=')[1]

  return nomeProduto
}

const server = http.createServer((request, response) => {

  // Usamos o modulo url para interpretar os pedacos da url.
  // Um request /busca?produto=abc vai gerar o seguinte objeto:
  //
  // Url {
  //   protocol: null,
  //   slashes: null,
  //   auth: null,
  //   host: null,
  //   port: null,
  //   hostname: null,
  //   hash: null,
  //   search: '?asfdsadf=asdf',
  //   query: 'asfdsadf=asdf',
  //   pathname: '/busca',
  //   path: '/busca?asfdsadf=asdf',
  //   href: '/busca?asfdsadf=asdf' }

  const rota = url.parse(request.url)

  // Se o request for na rota /produtos e for do tipo GET...
  if (request.method === 'GET' && rota.pathname === '/produtos') {
    response.write('Lista de produtos: \n')
    response.write(produtos.join('\n'))
    return response.end()
  }

  // Se o request for na rota /busca e for do tipo GET...
  if (request.method === 'GET' && rota.pathname === '/busca') {
    const produtoBuscado = pescaNomeDoProduto(rota)
    const produtosEncontrados = buscaProdutos(produtoBuscado)

    response.write('Lista de produtos: \n')
    response.write(produtosEncontrados.join('\n'))
    return response.end()
  }

  // Senao, erro 404...
  response.write('Rota não encontrada')
  return response.end()
})

// Vamos esperar requests na porta 3000
server.listen(3000, () => console.log('A aplicacao iniciou, visit http://localhost:3000/'))
```

Ao executarmos esse script, podemos acessar o endereço no navegador e então teremos algo como:

![Uma lista de produtos no navegador](/images/2018/05/04/busca-de-produtos.png)

Usando apenas o Node e os módulos `http` e `url` já conseguimos implementar as funcionalidades de nosso e-commerce, no entanto, para cada
nova rota, teríamos que adicionar um novo if e o código começa a ficar extenso... Vamos fazer exatamente a mesma coisa, só que dessa vez,
vamos usar o Express


## Reconstruindo a aplicacão com o Express

Antes de mais nada, temos que adicionar o express ao projeto. Os módulos que utilizamos anteriormente (http e url) fazem parte da biblioteca
padrão do Node, ou seja, eles vêm junto com o Node na hora da sua instalação e são compilados junto com o restante do código do Node (Mais
detalhes sobre isso [nesta discussão](https://stackoverflow.com/a/42892065)), por isso não foi necessário utilizar o NPM para instalá-los.
Este não é o caso do Express, já que ele é um módulo desenvolvido pela comunidade e está disponível no repositório do NPM nas internets.

Primeiro, então, configuramos o nosso projeto com o NPM:

```
npm init
```

Respondemos às perguntas do npm (podemos apenas apertar enter até o fim do questionário).

Então, adicionamos o `express` na lista das nossas dependências:

```
npm install --save express
```

Após a execução deste comando, teremos uma pasta chamada `node_modules`. Nesta pasta ficarão todos os módulos baixados pelo npm.

Agora que temos o `express` instalado, podemos começar a desenvolver a aplicação utilizando-o.

Podemos apagar tudo que está contido no arquivo server.js, para então escrevermos o novo código que usa o `express`. Para isso, escreveremos
o seguinte:

```javascript
const express = require('express')

const produtos = [
  'Balde',
  'Vassoura',
  'Copo',
  'Cavalo',
  'Relogio'
]

const buscaProdutos = (produtoBuscado) => produtos.filter(produto => produtoBuscado === produto)

const server = express()

server.get('/produtos', (request, response) => {
  response.send(
    'Lista de Produtos:\n' +
    produtos.join('\n'))
})

server.get('/busca', (request, response) => {
  const produtosEncontrados = buscaProdutos(request.query.nomeProduto)

  response.send(
    'Lista de Produtos:\n' +
    produtosEncontrados.join('\n'))
})

server.listen(3000, () => console.log('Acesse localhost:3000'))
```

E assim temos uma nova aplicação express.

Neste exemplo, o ganho trazido pelo Express pode parecer pequeno, mas, conforme a aplicação vai crescendo, o modelo do Express permite
escalar a aplicação de uma forma muito confortável.

Usar o Express nos traz muitas vantagens, no entanto, é preciso dedicar um pouquinho de tempo para entendê-lo melhor e conhecer onde seus
benefícios serão bem aproveitados. Essa parte fica para um próximo artigo. :)
