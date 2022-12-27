# The Road to GraphQL

A idéia deste repositório é documentar meus aprendizados ao longo dos estudos de GraphQL com base no livro [The Road to GraphQL](https://www.roadtographql.com/), de [Robin Wieruch](https://www.robinwieruch.de/).

## RESTful APIs

Antes do GraphQL, a solução mais utilizada para transferência de dados entre cliente e servidor eram APIs REST.

APIs REST disponibilizam URLs para recursos específicos (ex.: usuários, projetos, produtos, etc.) e tais recursos (ou entidades) possuem certos atributos (ex.: id, nome, data de criação, data da última atualização, etc.)

Em APIs REST, para cada recurso, podemos realizar diferentes operações, definidas por verbos (ex.: GET, POST, PUT, DELETE, etc.)

Além disso, quando a aplicação cliente necessita de dados de diferentes entidades (ex.: últimos 5 **blog posts** do **usuário** 23), múltiplas requisições devem ser realizadas (ex.: `GET /users/23` e `GET /blogs?user_id=23&limit=5`).

Um problema dessa abordagem (dependendo to contexto) pode ser o que em inglês descreve-se como **_overfetching_** (o qual traduzo livremente como excesso de chamadas via rede).

### Estudo de caso exemplo - O problema do _overfetching_

Imagine que os dados de certas entidades (ex.: usuários e blogs) estão disponíveis em um mesmo servidor e diferentes aplicações cliente consomem dados deste servidor através de sua API. No entanto, estes diferentes clientes tem diferentes necessidades, ou até mesmo diferentes limitações.

Uma aplicação web desktop, por exemplo, tem o "luxo" de ter mais espaço em tela para exibir informações. Além disso, em certos casos, os dispositivos que rodam tais aplicações possuem mais poder computacional, ou até mesmo, internet com velocidade mais rápida (ex.: rede de fibra ótica).

Neste caso, fazer mais de uma requisição para buscar informações à serem exibidas na tela pode não ser um grande problema.

Além disso, visto o maior espaço em tela, mais campos podem ser exibidos, ou seja, mais atributos das entidades.

Já uma aplicação cliente para dispositivos móveis tem que lidar com telas menores, e por exemplo, com uma internet de velocidade lenta (ex.: 3G). Neste caso, há um grande custo em cada requisição feita pela rede.

Neste caso, nem todos os atributos retornados por tais requisições poderão ser exibidos na tela, visto a limitação de seu tamanho. Neste caso, só os atributos mais importantes são exibidos e os outros atributos trafegados pela rede foram um desperdício.

Com isso, no mundo REST, para resolver tais situações, APIs específicas por cliente devem ser desenvolvidas  para evitar desperdícios. Porém, isso gera mais código pra ser mantido.

Por fim, no mundo das APIs REST, o lado do servidor é quem define o que está disponível para seus clientes.

## Exemplos de APIs REST

Segue abaixo uma lista de APIs REST:

- https://api.gocd.org/current/
- https://developer.typeform.com/
- https://openweathermap.org/api

## [GraphQL](https://graphql.org)

GraphQL (ou _Graph Query Language_) é uma linguagem _open-source_ criada pelo Facebook que resolve alguns dos problemas do mundo das APIs REST, onde é possível fazer uma única chamada via rede para buscar exatamente os dados que fazem sentido cada aplicação cliente (mesmo que tais dados provenham de diferentes entidades), evitando o problema do _overfetching_.

Em uma API GraphQL, só uma URL é disponível, e em vez de operações por verbos (como GET, POST, etc.), temos operações de _queries_ (leitura) e _mutations_ (escrita).

Em GraphQL, entidades possuem campos. Além disso, GraphQL é uma linguagem fortemente tipada, portanto, cada campo possui seu proprio tipo (os quais podem ser tipos primitivos, como String, Int, Boolean, ou tipos customizados, como User, por exemplo).

Diferente de APIs REST, com GraphQL a perspectiva muda, onde as aplicações cliente são quem decide o que querem, em vez do servidor.

No lado do "servidor", o que temos são esquemas GraphQL (_GraphQL Schemas_), os quais podem ser múltiplos (por exemplo, em uma arquitetura de micro-servições), e um esquema consolidado pode ser criado para consumo pela aplicação principal (utilizando o mecanismo de _GraphQL Schema Stitching_).

## Exemplos de APIs GraphQL

A [API GraphQL do GitHub](https://docs.github.com/pt/graphql) será utilizada para os estudos a seguir.

### [GitHub GraphQL API Explorer](https://docs.github.com/pt/graphql/overview/explorer)

Para explorar a API GraphQL do GitHub, o _GitHub GraphQL API Explorer_ será utilizado, como uma alternativa que roda no navegador, sem a necessidade de nada mais além de uma conta no GitHub.

Abaixo seguem exemplos de _queries_ com suas devidas explicações.

...