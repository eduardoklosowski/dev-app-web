Capítulo 04 - Utilizando APIs
=============================

Com o Ajax, além de carregar partes dinamicamente na aplicação Web, possibilitou a utilização do que é chamado de API, como determinados endereços para efetuar cadastros, consultas, edição e exclusão. De forma geral, quando um serviço é exposto por uma API, vários programas podem interagir com ela, a fim de oferecer uma interface para o usuário executar suas atividades.


API REST
--------

Uma API REST é uma API que segue alguns princípios, como endereços simples e intuitivos para acesso a determinado recursos, utilização do método da requisição HTTP para determinar a ação a ser efetuada, e assim por diante.

Alguns exemplos de endereços podem ser vistos a baixo:

======  ==========  ==============================================
Método  Endereço    Descrição
======  ==========  ==============================================
GET     /clientes   Listar clientes cadastrado no sistema.
POST    /cliente    Cadastra um novo cliente.
GET     /cliente/1  Busca os dados do cliente número ``1``.
PUT     /cliente/1  Altera as informações do cliente número ``1``.
DELETE  /cliente/1  Apaga o cliente número ``1``.
======  ==========  ==============================================

Nestes endereços os dados do cliente, por exemplo no cadastro, podem ser enviados para o servidor através de parâmetros da URL ou no corpo da requisição.

O corpo da resposta destas requisições podem ser uma página com as informações, porém o mais comum é a utilização de alguma linguagem facilmente lida por computador, uma vez que permite o acesso de uma informação diretamente. Linguagens de marcação são ótimas para isso, como o XML, porém esta perdendo espaço para o Json, que é a notação de objetos do JavaScript. Desenvolvendo uma aplicação Web, que terá código JavaScript, se a API responder em Json, muitas coisas serão facilitadas, como a transformação da resposta em Objetos do JavaScript.


Templates JavaScript
--------------------

Uma API REST na maioria dos casos retorna apenas as informações, ou seja, quem fez a requisição deve tratá-los para fazer sua exibição. A principal vantagem para o servidor é que ele não terá que fazer este processamento. A transformação dos dados em uma página HTML, embora rápida, quando feita pra milhares de páginas representa uma carga considerável no servidor, transferir este trabalho para o cliente pode ser uma boa estratégia.

Partindo deste princípio, surgiram diversas linguagens de templates para JavaScript, sujo a principal funcionalidade é receber dados e transformá-los numa página HTML. Isso quando feito dentro do navegador permite uma alternativa interessante junto com a API REST em relação a geração da página no servidor.
