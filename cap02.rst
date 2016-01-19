Capítulo 02 - Código executado no servidor
==========================================

Entre a requisição e a resposta do HTTP muita coisa pode ocorrer. Além de buscar um arquivo em disco e enviá-lo pela rede, outros processos podem serem feitos, já que o protocolo define como é a requisição e resposta, porém não como uma resposta deve ser gerada, isso abre opções para criar páginas dinâmicas.

Uma página dinâmica é uma página que tem o seu conteúdo gerado quando uma requisição é feita. Esse processo é feito através de um programa que identifica as informações da requisição HTTP, baseado num algoritmo gera a resposta.


CGI
---

O primeiro método padronizado para gerar páginas dinâmicas e mais simples é o CGI. Seu funcionamento em vez de carregar um arquivo, executa o mesmo, passando as informações do cabeçalho como variáveis de ambiente e corpo na entrada padrão. O conteúdo da resposta é feita através da saída padrão, com as informações de cabeçalho, uma linha em branco e o corpo da resposta. Sendo o campo ``Content-Type`` obrigatório na resposta.

O CGI é independente de linguagem de programação, já que qualquer linguagem que permita controlar a entrada padrão, saída padrão e ler as variáveis de ambiente pode ser utilizada. Um exemplo em shell script pode ser visto a baixo:

.. code-block:: sh
  :linenos:

  #!/bin/bash
  echo "Content-Type: text/html; charset=utf-8"
  echo
  echo "<h1>Computador: $HOSTNAME</h1>"

Existem vários servidores que suportam CGI, inclusive o servidor da biblioteca padrão do Python, quando chamado com o parâmetro ``--cgi``. Porém sempre é bom verificar como o servidor em questão funciona, neste servidor as páginas tem que estar no diretório chamado ``/cgi-bin/``, em outros o arquivo deve ter a extensão ``.cgi``.

Salvando o código a cima com o nome de ``index.cgi`` dentro do diretório ``cgi-bin``, conforme a árvore a baixo:

.. code-block:: none

  (servidor)
   ` cgi-bin/
     ` index.cgi

Executando o script diretamente, uma saída possível é a seguinte:

.. code-block:: none

  Content-Type: text/html; charset=utf-8

  <h1>Computador: notebook</h1>

Porém quando iniciado o servidor HTTP e feita a requisição a saída é outra:

.. code-block:: sh

  python -m http.server --cgi
  http http://localhost:8000/cgi-bin/index.cgi

.. code-block:: http

  HTTP/1.0 200 Script output follows
  Content-Type: text/html; charset=utf-8
  Date: Sun, 27 Dec 2015 23:49:35 GMT
  Server: SimpleHTTP/0.6 Python/3.4.2

  <h1>Computador: notebook</h1>

Um exemplo de script acessando variáveis de ambiente:

.. code-block:: python
  :linenos:

  #!/usr/bin/env python
  import os

  print('Content-Type: text/plain')
  print()
  for key in ('REQUEST_METHOD', 'SCRIPT_NAME', 'REMOTE_ADDR', 'HTTP_USER_AGENT'):
      print('%s => %s' % (key, os.environ.get(key)))

.. code-block:: http

  HTTP/1.0 200 Script output follows
  Content-Type: text/plain
  Date: Mon, 28 Dec 2015 00:03:42 GMT
  Server: SimpleHTTP/0.6 Python/3.4.2

  REQUEST_METHOD => GET
  SCRIPT_NAME => /cgi-bin/index.cgi
  REMOTE_ADDR => 127.0.0.1
  HTTP_USER_AGENT => HTTPie/0.9.2

Em Python existem duas bibliotecas que auxiliam no desenvolvimento de scripts CGI, sendo `cgi <https://docs.python.org/3/library/cgi.html>`_ e `cgitb <https://docs.python.org/3/library/cgitb.html>`_.


Fast-CGI
--------

Scripts CGI são simples e fáceis de implementar, porém sua utilização tem problemas com desempenho, uma vez que cada página acessada, gera um processo diferente que tem que ser executado. Visando a resolver esse problema existe o Fast-CGI, que manter um processo sempre em execução, quando uma requisição HTTP chega no servidor o mesmo comunica com esse processo via socket num protocolo semelhante ao HTTP.

As vantagens deste método é que requisição HTTP não precisa necessariamente iniciar um processo novo. Também como a comunicação ocorre via socket, pode ser feita via unixsocket, quando no mesmo computador, ou via TCP, podendo estar em outro computador para dividir a carga. Porém sua implementação não é simples, uma vez que cria um protocolo próprio e precisa trabalhar com multiplexação de requisições.

Atualmente essa é uma alternativa que vem competindo com o ``mod_php`` do Apache. Juntando o NGINX que não tem suporte ao PHP por padrão, mas implementa o Fast-CGI, com o PHP FPM, entrega uma alternativa interessante de servidor.


WSGI
----

Python por sua vez criou um padrão próprio chamado `WSGI <https://wsgi.readthedocs.org/en/latest/>`_, que é definido pela `PEP 333 <https://www.python.org/dev/peps/pep-0333/>`_, sendo mais tarde substituída pela `PEP 3333 <https://www.python.org/dev/peps/pep-3333/>`_. Sua principal característica é que o interpretador Python é executado dentro do servidor, sendo que as informações das requisições são passadas a uma única função, que processa e devolve a resposta.

A função do WSGI recebe dois parâmetros. No primeiro as informações da requisição são passadas, semelhante as variáveis de ambiente do CGI. No segundo uma função que recebe as informações do cabeçalho da resposta. O corpo da mesma é um interável que a função deve retornar.

Um exemplo de código WSGI, utilizando o servidor de referência da biblioteca padrão, pode ser visto a baixo:

.. code-block:: python
  :linenos:

  def application(environ, start_response):
      start_response('200 OK', [('Content-Type', 'text/html; charset=utf-8')])
      yield '<h1>Teste<h1>'.encode('utf-8')


  if __name__ == '__main__':
      from wsgiref.simple_server import make_server

      httpd = make_server('127.0.0.1', 8000, application)
      print('Servidor rodando em http://127.0.0.1:8000/ ...')
      httpd.serve_forever()

Quando a aplicação Web for colocada em produção, outro servidor deve ser utilizado. O `gunicorn <http://gunicorn.org/>`_ é um servidor HTTP simples que só funciona com WSGI, pode ser instalado com o ``pip`` e iniciado via linha de comando.

.. code-block:: sh

  pip install gunicorn
  gunicorn -b 127.0.0.1:8000 --access-logfile - --error-logfile - wsgi:application

.. note::
  O ``wsgi:application`` significa que a função WSGI se chama ``application`` e está no módulo ``wsgi``, neste caso o arquivo ``wsgi.py``.

O WSGI também pode ser utilizado semelhante ao Fast-CGI num servidor HTTP, a principal diferença é que o protocolo de comunicação é o HTTP e por isso deve ser configurado como um proxy reverso. Desta forma mantem as vantagens, como poder executar o código em outro computador, ou até configurar vários servidores para responder as solicitações, fazendo balanceamento de carga.


Middleware
##########

Como o WSGI é uma função como qualquer outra em Python, é possível tirar proveito da flexibilidade da linguagem para implementar algumas funcionalidades. Middleware são funções que substitui a função da aplicação, podendo executar algo antes ou depois da aplicação, como verificar os cookies e adicionar no ``environ`` qual o usuário que está autenticado ou gravar o tempo que as páginas levam para serem geradas, fazendo uma análise de desempenho.

Como quase todos os frameworks Web de Python são baseados em WSGI, esses middlewares podem ser utilizados em conjunto com qualquer um desses frameworks.


Como programar para Web na linguagem ou framework X?
----------------------------------------------------

Esses métodos de gerar páginas dinâmica são apenas alguns exemplos, porém quando se programa numa linguagem ou framework isso é abstraido. No PHP o código que executa como CGI, Fast-CGI ou no Apache com o ``mod_php`` é o mesmo, diferenciando apenas o interpretador da linguagem. No Python quase todos são baseados em WSGI, podendo a partir dele criar alternativas para outros métodos, porém mesmo assim não é comum a manipulação da função WSGI diretamente.

Não importa qual a linguagem ou framework utilizado para desenvolver uma aplicação Web, todas elas irão oferecer uma forma de se acessar as informações da requisição, podendo ter mais facilidades e funcionalidades prontas, isso é a única coisa que vai variar no código de um ou outro, além da própria linguagem.

Também é preciso pensar em como a aplicação funcionará em produção, para saber o que pode influenciar de acordo com a configuração do servidor HTTP.


Exercícios
----------

1. Crie um script CGI com uma página HTML, teste o acesso com ``Content-Type: text/html`` e ``Content-Type: text/plain``, vendo a diferença na renderização do navegador.
2. Crie um script WSGI com uma página HTML.


Discussão
---------

1. Como fazer para gerar páginas diferentes a partir do mesmo sccript WSGI?
2. Como enviar imagens ou outros arquivos estáticos através do WSGI? Isso é eficiente?
