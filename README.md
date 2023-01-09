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

Além disso, nem todos os atributos retornados por tais requisições poderão ser exibidos na tela, visto a limitação de seu tamanho. Neste caso, só os atributos mais importantes são exibidos e os outros atributos trafegados pela rede foram um desperdício.

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

No lado do "servidor", o que temos são esquemas GraphQL (_GraphQL Schemas_), os quais podem ser múltiplos (por exemplo, em uma arquitetura de micro-serviços), e um esquema consolidado pode ser criado para consumo pela aplicação principal (utilizando o mecanismo de _GraphQL Schema Stitching_).

## Exemplos de APIs GraphQL

A [API GraphQL do GitHub](https://docs.github.com/pt/graphql) será utilizada para os estudos a seguir.

### [GitHub GraphQL API Explorer](https://docs.github.com/pt/graphql/overview/explorer)

Para explorar a API GraphQL do GitHub, o _GitHub GraphQL API Explorer_ será utilizado, como uma alternativa que roda no navegador, sem a necessidade de nada mais além de uma conta no GitHub.

### Mão-na-massa

Abaixo seguem exemplos de _queries_ com suas devidas explicações e retornos.

#### _Query_ 1 - Busca o valor do campo `login` a partir da entidade `viewer`

```graphql
query {
  viewer {
    login
  }
}

```

##### Retorno da `query` 1

```json
{
  "data": {
    "viewer": {
      "login": "wlsf82"
    }
  }
}

```

#### _Query_ 2 - Busca os valores `login`, `name` e `url` a partir da entidade `viewer`

```graphql
query {
  viewer {
    login
    name
    url
  }
}

```

##### Retorno da `query` 2

```json
{
  "data": {
    "viewer": {
      "login": "wlsf82",
      "name": "Walmyr",
      "url": "https://github.com/wlsf82"
    }
  }
}

```

#### _Query_ 3 - Busca os valores `name` e `url` a partir da entidade `organization`, passando o `login` de tal entidade como parâmetro da `query`

```graphql
query {
  organization(login: "facebook") {
    name
    url
  }
}

```

##### Retorno da `query` 3

```json
{
  "data": {
    "organization": {
      "name": "Meta",
      "url": "https://github.com/facebook"
    }
  }
}

```

#### _Query_ 4 - Busca os valores `name` e `url` a partir da entidade `organization`, porém, para duas organizações, portanto, dando alias para cada uma, para evitar conflito de campos

```graphql
query {
  facebook: organization(login: "facebook") {
    name
    url
  }
  cypress: organization(login: "cypress-io") {
    name
    url
  }
}

```

##### Retorno da `query` 4

```json
{
  "data": {
    "facebook": {
      "name": "Meta",
      "url": "https://github.com/facebook"
    },
    "cypress": {
      "name": "Cypress.io",
      "url": "https://github.com/cypress-io"
    }
  }
}

```

#### _Query_ 5 - Busca os valores `name` e `url` a partir da entidade `organization`, para duas organizações, portanto, dando alias para cada uma, porém, usando um fragmento, para evitar duplicação de código

```graphql
query {
  facebook: organization(login: "facebook") {
    ...orgSharedFields
  }
  cypress: organization(login: "cypress-io") {
    ...orgSharedFields
  }
}

fragment orgSharedFields on Organization{
  name
  url
}

```

##### Retorno da `query` 5

```json
{
  "data": {
    "facebook": {
      "name": "Meta",
      "url": "https://github.com/facebook"
    },
    "cypress": {
      "name": "Cypress.io",
      "url": "https://github.com/cypress-io"
    }
  }
}

```

#### _Query_ 6 - Busca valores em comum a partir da entidade `organization` com o uso de fragmentos, em conjunto com dados específicos para um determinado objeto da `query`

```graphql
query {
  facebook: organization(login: "facebook") {
    ...orgSharedFields
    login
  }
  cypress: organization(login: "cypress-io") {
    ...orgSharedFields
  }
}

fragment orgSharedFields on Organization{
  name
  url
}

```

##### Retorno da `query` 6

```json
{
  "data": {
    "facebook": {
      "name": "Meta",
      "url": "https://github.com/facebook",
      "login": "facebook"
    },
    "cypress": {
      "name": "Cypress.io",
      "url": "https://github.com/cypress-io"
    }
  }
}

```

#### Query 7 - Busca valores da entidade `organization` onde o parâmetro `login` é obrigatório e passado via uma variável `$org`

```graphql
query ($org: String!) {
  organization(login: $org) {
    name
    url
  }
}

```

##### Definição da variável para a `query` 7

```json
{
  "org": "cypress-io"
}

```

##### Retorno da `query` 7

```json
{
  "data": {
    "organization": {
      "name": "Cypress.io",
      "url": "https://github.com/cypress-io"
    }
  }
}

```

#### Query 8 - Busca valores da entidade `organization` onde o parâmetro `login` NÃO é obrigatório, a variável `$org` possui um valor _default_, porém, tal variável NÃO está definida

```graphql
query ($org: String = "cypress-io") {
  organization(login: $org) {
    name
    url
  }
}

```

##### Definição da variável para a `query` 8

```json
{}

```

##### Retorno da `query` 8

```json
{
  "data": {
    "organization": {
      "name": "Cypress.io",
      "url": "https://github.com/cypress-io"
    }
  }
}

```

#### Query 9 - Busca valores da entidade `organization` onde o parâmetro `login` NÃO é obrigatório, a variável `$org` possui um valor _default_, porém, tal variável está definida com um valor DIFERENTE do valor _default_

```graphql
query ($org: String = "cypress-io") {
  organization(login: $org) {
    name
    url
  }
}

```

##### Definição da variável para a `query` 9

```json
{
  "org": "facebook"
}

```

##### Retorno da `query` 9

```json
{
  "data": {
    "organization": {
      "name": "Meta",
      "url": "https://github.com/facebook"
    }
  }
}

```

#### _Query_ 10 - Busca os valores `name` e `url` a partir da entidade `organization`, para duas organizações, portanto, dando alias para cada uma. Além disso, usa dois fragmentos sob a mesma entidade (`Organization`) para evitar duplicação de código e exemplificar o uso de mais de um fragmento na mesma consulta

```graphql
query {
  facebook: organization(login: "facebook") {
    ...sharedFields1
    ...sharedFields2
  }
  cypress: organization(login: "cypress-io") {
    ...sharedFields1
    ...sharedFields2
  }
}

fragment sharedFields1 on Organization{
  name
}

fragment sharedFields2 on Organization{
  url
}

```

##### Retorno da `query` 10

```json
{
  "data": {
    "facebook": {
      "name": "Meta",
      "url": "https://github.com/facebook"
    },
    "cypress": {
      "name": "Cypress.io",
      "url": "https://github.com/cypress-io"
    }
  }
}

```
