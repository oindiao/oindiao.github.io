---
layout: post
title:  "Swagger: Exportando uma collection"
date:   2020-09-14 06:00:00 -0300
categories: programação
---

![Logo do swagger: círculo verde, com três pontos entre chaves { }. Depois está escrito Swagger.](/images/2020-09-14/01.png)

Postman, Insomnia, Soap Ui e outros programas que são usados para realizar chamadas em API Rest têm uma opção de importar arquivos, ou url’s, vindos do Swagger, ou de outras bibliotecas de documentação, e assim minimizando erros de copiar/colar do swagger, além de ser mais rápido que criar todas as chamadas.

Geralmente essa url fica em ***/v2/api-docs***

![Print do título da tela do swagger, com destaque em /v2/api-docs](/images/2020-09-14/02.png)

Se abrirmos esse link do exemplo, veremos:

![Conteúdo da página /v3/api-docs](/images/2020-09-14/03.png)

Esta conteúdo no formato Json contém toda a documentação do Swagger. Essa documentação segue um padrão, o [Open API](https://www.openapis.org/ "Open API"){:target="_blank"} e, assim, as ferramentas que fazem requisições em API Rest conseguem importar, sem problema algum.

Para API de exemplo, usarei uma API desenvolvida para o curso de *DTAR*, [Descomplicando testes de API Rest.](https://hotmart.com/product/descomplicando-testes-de-api-rest "Descomplicando testes de API Rest."){:target="_blank"}, ministradas pelo Antonio Montanha e Júlio de Lima, que se encontra no repositório do GitHub abaixo:

[Gerenciador Viagem](https://github.com/AntonioMontanha/gerenciador-viagens "Gerenciador Viagem"){:target="_blank"}

Usando o Postman como exemplo, vamos fazer a importação.
Na tela de ***Import*** (***File -> Import*** ou na tela mesmo, que tem um botão ***Import***), clicar na aba Link:

![Tela de import do Postman, na aba link](/images/2020-09-14/04.png)

Agora coloque a url completa do **/v2/docs**

![Tela de import com a url do /v2/api-docs](/images/2020-09-14/05.png)

Depois clicar em ***Continue***. Após isso, ira aparecer uma tela para ***double-check***, com formatação da identação, etc.

![tela após clicar em Continue, para double-check](/images/2020-09-14/06.png)

Agora clique em ***Import***. Assim, todo esquema é importado.

![Tela do postman com a collection importada](/images/2020-09-14/07.png)

![Um post da collection aberta no postman](/images/2020-09-14/08.png)

Pronto. Sua vida facilidade ao realizar requisições e testes em APIs Rest.
Como falado, esse exemplo foi usando Postman, mas outros programas também possuem essa funcionalidade. 

Dê uma procurada no [Google](https://wwww.google.com.br "Google"){:target="_blank"} como fazer essa importação para sua ferramenta favorita.