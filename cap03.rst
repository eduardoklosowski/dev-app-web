Capítulo 03 - Código executado no navegador
===========================================

Uma aplicação Web é composta de vários elementos, como textos, imagens, efeitos visuais e código. Todos esses elementos encontram-se no servidor, bastando o cliente requisitá-los para que a aplicação possa ser executada no navegador.


HTML
----

O HTML é uma linguagem de marcação utilizada para descrever o conteúdo das páginas, além de indicar o endereço dos recursos complementares. Para que tal tarefa seja executada, esta linguagem é composta de marcações, também conhecidas como *tags*, que indicam o início e fim de um menu, texto, parágrafo, link e qualquer outro elemento que se possa imaginar.

O estudo das *tags* HTML, bem como seus atributos, está além do escopo deste texto. Porém existem excelentes documentações na internet sobre este assunto, como a `MDN (Mozilla Developer Network) <https://developer.mozilla.org/pt-BR/>`_, que apesar de ter o nome da Mozilla que desenvolve o Firefox, descreve o padrão e peculariedades de outros motores Web além do Gecko (utilizado no Firefox), como o Webkit, que é a base de muitos navegadores.

Uma relação das *tags* do HTML pode ser encontrada em https://developer.mozilla.org/en-US/docs/Web/HTML/Element, e uma relação dos atributos pode ser encontrada em https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes.

Também é importante observar que o HTML tem duas sessões distintas, ``head`` e ``body``. No ``head`` encontra-se algumas informações como título da página, ou meta-informações, porém que não aparecem diretamente na página. Diferente do ``body`` que contem o conteúdo da página em si. Um exemplo de página HTML na sua versão 5, pode ser vista a baixo:

.. code-block:: html
  :linenos:

  <!DOCTYPE html>
  <html>
    <head>
      <title>Teste</title>
      <meta charset="utf-8">
    </head>
    <body>
      <h1>Teste</h1>
    </body>
  </html>


CSS
---

É possível fazer uma página apenas com HTML, porém como ele foi desenvolvido inicialmente para publicação de textos científicos, o visual padrão não é o melhor para todos os tipos de aplicações. Para permitir a customização da forma como os elementos são exibidos na tela foi criado o CSS, que funciona como um guia de estilos, onde algumas propriedades dos elementos são alteradas, quando os mesmos se encaixam nas regras do arquivo CSS, como pode-se ver no arquivo abaixo:

.. code-block:: css
  :linenos:

  body {
    font-family: sans-serif;
    font-size: 12px;
    color: #333333;
    background-color: #ffffff;
  }

  h1 {
    font-family: serif;
    color: #000000;
  }

As regras e propriedades do CSS também estão além do escopo deste texto, porém existe bastante material na MDN sobre o assunto.


JavaScript
----------

Com HTML e CSS é possível criar o conteúdo de uma página e alterar sua forma de exibição, porém todas as informações estão estáticas neste arquivos, não permitindo que quem controla o navegador interagir com elas. O JavaScript, diferente das outras duas é uma linguagem de programação, permitindo que coisas mais interativas ocorram durante a exibição de uma página, uma vez que pode ser executado diretamente no navegador do cliente.

Como o código JavaScript é executado durante a exibição de uma página, não é necessário a comunicação com o servidor para fazer alterações na própria página. Um exemplo pode ser visto a baixo:

.. code-block:: html
  :linenos:

  <!DOCTYPE html>
  <html>
    <head>
      <title>Soma</title>
      <meta charset="utf-8">
    </head>
    <body>
      <input id="n1" type="number" value="0"> +
      <input id="n2" type="number" value="0"> =
      <output id="res">0</output>

      <script>
        function somar() {
          var n1 = document.getElementById('n1');
          var n2 = document.getElementById('n2');
          var res = document.getElementById('res');

          res.value = parseInt(n1.value) + parseInt(n2.value);
        }

        (function() {
          document.getElementById('n1').addEventListener('change', somar);
          document.getElementById('n2').addEventListener('change', somar);
        }());
      </script>
    </body>
  </html>

Como pode ser visto, o JavaScript faz alterações na página, buscando os elementos do HTML, esse processo chama-se manipulação de DOM, uma vez que a cópia da página HTML que está em execução no navegador recebe o nome de DOM.

Atualmente a única linguagem de programação que é possível sua execução nos navegadores é o JavaScript, porém existem alguns projetos como o CoffeScript, Brython e outros que ou são bibliotecas em JavaScript ou compilam para JavaScript, permitindo assim sua execução no navegador. Porém como no final o que está sendo executado é JavaScript, tendem a serem mais lentas e criam uma camada a mais que pode dificultar a identificação de problemas.

Com essas linguagens que compilam para JavaScript, nem sempre o código resultante é muito legível por humanos, dificultando o entendimento do que realmente está sendo executado no navegador, podendo assim também ocultar código malicioso.


Ajax
----

O fluxo inicial de carregamento de uma página é o cliente requisitar o HTML, olhar a resposta e quando identificar o endereço de recurso externo a mesma (imagens, CSS e JavaScript por exemplo), requisitá-lo. Isso gera uma grande comunicação quando a página está carregando, porém uma vez que ela foi carregada não ocorrem mais requisições.

Com Ajax isso é diferente, via código JavaScript é possível criar novas requisições, como quando um botão for pressionado, ou formulário for preenchido, porém sem ter que recarregar toda a página. A principal vantagem é que essas requisições ocorrem em *background* e o usuário pode continuar interagindo com a página enquanto isso.

O código abaixo mostra o carregamento dinâmico da página utilizando Ajax pela biblioteca jQuery:

.. code-block:: html
  :linenos:

  <!DOCTYPE html>
  <html>
    <head>
      <title>Ajax</title>
      <meta charset="utf-8">
    </head>
    <body>
      <ul>
        <li id="pg1">Página 1</li>
        <li id="pg2">Página 2</li>
      </ul>
      <div id="conteudo"></div>
      <script src="jquery.min.js"></script>
      <script>
        function loadPage(page) {
          return function(evt) {
            $.ajax({
              method: 'GET',
              url: page,
              success: function(date) {
                document.getElementById('conteudo').innerHTML = date;
              }
            });
          }
        }
        document.getElementById('pg1').addEventListener('click', loadPage('pg1.html'));
        document.getElementById('pg2').addEventListener('click', loadPage('pg2.html'));
      </script>
    </body>
  </html>


Acompanhar a execução no navegador
----------------------------------

Um recurso que foi desenvolvido e que foi desenvolvido e integrado a pouco tempo é a possibilidade de fazer o acompanhamento da execução da aplicação Web diretamente no navegador. Firefox, Chrome e Internet Explorer possuem suas ferramentas para desenvolvimento, podem variar as funcionalidades e recursos disponíveis, porém envolve visualizar e manipular o DOM, CSS e JavaScript, além de acompanhar as requisições HTTP efetuadas, bem como suas respostas e tempo para identificar possíveis problemas de desempenho.

A utilização destes recursos no Firefox é extremamente simples, bastando pressionar ``Ctrl + Shift + I`` para abrir as ferramentas de desenvolvimento. Também é possível clicar com o botão direito em cima de um elemento da página e na opção ``Inspecionar elemento`` já abre o DOM com o elemento selecionado. Se alguma funcionalidade da página não estiver funcionando corretamente, a aba ``Console`` pode trazer alguma mensagem de erro que ajude a identificá-lo e corrigi-lo.


Exercícios
----------

1. Crie uma página HTML com arquivos CSS e JavaScript separados do HTML principal e acompanhe as requisições na aba ``Rede`` do Firefox.
2. Faça alterações na página HTML através do JavaScript e da aba ``Inspecionar`` do Firefox.
3. Faça requisições Ajax e acompanhe as mesmas através da aba ``Rede`` do Firefox.


Discussão
---------

1. Qual a vantagem de ter os arquivos CSS e JavaScript juntos no HTML? Qual a vantagem de deixa-los separados?
2. O que são imagens *inline*?
3. O que é a minificação do JavaScript? Quais suas vantagens e desvantagens? Esse processo é reversível?
4. Por quê páginas que usam Ajax funcionam apenas no servidor, enquanto páginas sem Ajax funcionam abrindo o HTML diretamente no navegador?
5. Qual o motivo da frase abaixo ser falsa?

  Links com ``/imagens/foto.jpg`` só funcionam quando abertos via um servidor, enquanto links como ``imagens/foto.jpg`` funcionam tanto com servidor, quanto abrindo o HTML diretamente.
