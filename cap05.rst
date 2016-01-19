Capítulo 05 - Arquitetura de aplicações Web
===========================================

Além do código em si e como será executado no cliente, existem várias extratégias de como será disponibilizado pelo servidor.


Servidor
--------

Além da discussão de qual programa de servidor HTTP será utilizado, também é possível criar arquiteturas com diversos serviços. Alguns exemplos serão discutidos na sequência.


Servidor Único
##############

A estratégia mais simples é a configuração de um único servidor, que responderá a todas as requisições. Por não ter muitos elementos, pode ser a forma mais fácil e rápida de disponibilizar uma aplicação. Porém pode apresentar problemas quando a quantidade de requisições aumentar muito, e ter dificuldades para escalar depois, quando se pensar em configurar um segundo servidor para fazer balanceamento de carga.


Proxy reverso e balenceamento de carga
######################################

Outra estratégia é utilizar um ou mais servidores para receber as requisições, porém em vez de processá-las, eles enviam-nas para diferentes servidores que fazem o trabalho, possibilitando o balanceamento das requisições. Também é possível não utilizar esses servidores que distribuiem as requisições e fazer isso via DNS, ou utilizar o DNS para fazer o balenceamento para esses servidores. Porém a vantagem de se ter servidores para distribuir as requisições é a posibilidade de já responder arquivos estáticos, que não precisam de processamento, ou ainda manter algumas respostas em cache, reduzindo a quantidade de requisições que realmente precisam serem processadas.

Os servidores que geram as páginas dinâmicas podem responder a diferentes partes da aplicação, sendo um caso mais simples, porém ainda poderia apresentar os problemas de escalabilidade da estratégia de servidor único. Também é possível ter diferentes servidores que podem gerar as mesma partes, e assim ter maior escalabilidade.

Como nem sempre é possível ter certeza que todas as requisições de um cliente irão para o mesmo servidor, a utilização de recursos locais do servidor ou a guarda de estado local não é recomendável, já que uma vez que a requisição chegue em outro servidor, estes recursos não estarão disponíveis. Desta forma a ideia de serviços é uma ótima opção, já que todos esses servidores podem utilizar o mesmo serviço e assim compartilhar as informações.

Esta ideia de múltiplos servidores com proxy reverso e utilização de serviços é fortemente baseada em PaaS, como os baseados em CF, ou até mesmo contâiners. Para melhor entendimento de como uma aplicação adaptada para PaaS deve ser desenvolvida existe o texto `12 fatores <http://12factor.net/pt_br/>`_.
