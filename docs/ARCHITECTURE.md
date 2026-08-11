# Arquitetura — Marketplace de Economia Circular Vortex

## 1. Objetivo deste documento

Este documento define as decisões técnicas oficiais do projeto.

Seu objetivo é evitar:

* decisões arquiteturais inconsistentes durante o desenvolvimento;
* introdução desnecessária de tecnologias;
* mudanças silenciosas feitas por ferramentas de IA;
* overengineering;
* divergências entre Backend e Frontend.

O arquivo `REQUIREMENTS.md` define **o que deve ser construído**.

Este arquivo define **como o sistema será estruturado tecnicamente**.

O contrato HTTP detalhado será definido posteriormente em `API_CONTRACT.md`.

---

# 2. Princípios arquiteturais

O projeto deve priorizar:

1. simplicidade;
2. clareza;
3. facilidade de manutenção;
4. facilidade de teste;
5. segurança adequada ao escopo;
6. rapidez de desenvolvimento;
7. facilidade de explicação durante a avaliação técnica.

O projeto NÃO deve utilizar complexidade arquitetural sem necessidade concreta.

Não serão utilizados:

* microserviços;
* mensageria;
* arquitetura distribuída;
* event sourcing;
* CQRS;
* Redis;
* Kafka;
* RabbitMQ;
* Kubernetes;
* arquitetura hexagonal completa;
* abstrações criadas apenas para demonstrar conhecimento técnico.

A aplicação será um **monólito modular simples**.

---

# 3. Estrutura geral

O projeto será mantido em um monorepo:

```text
vortex-project/
│
├── backend/
├── frontend/
├── docs/
├── .agent/
├── AGENTS.md
└── README.md
```

Backend e Frontend serão aplicações independentes, mas farão parte da mesma solução e do mesmo repositório Git.

A comunicação entre eles ocorrerá exclusivamente através da API HTTP.

---

# 4. Arquitetura geral da aplicação

```text
┌────────────────────────────┐
│                            │
│      Frontend / PWA        │
│      React + TypeScript    │
│                            │
└─────────────┬──────────────┘
              │
              │ HTTP / JSON
              │
              ▼
┌────────────────────────────┐
│                            │
│       Backend REST         │
│       Spring Boot          │
│                            │
└─────────────┬──────────────┘
              │
              │ JPA
              │
              ▼
┌────────────────────────────┐
│                            │
│        PostgreSQL          │
│                            │
└────────────────────────────┘
```

---

# 5. Backend — Stack principal

## Linguagem

Java 21.

## Framework

Spring Boot 4.1.x.

A versão inicial prevista é:

`4.1.0`

Mudanças de versão deverão ser justificadas e registradas.

## Build

Maven.

O projeto deverá incluir Maven Wrapper:

```text
mvnw
mvnw.cmd
.mvn/
```

Os comandos documentados deverão preferencialmente utilizar o Wrapper.

---

# 6. Dependências planejadas do Backend

Utilizar apenas dependências que possuam função clara no projeto.

Dependências principais:

* Spring Web / Spring MVC;
* Spring Data JPA;
* Spring Security;
* Spring Validation;
* Spring OAuth2 Resource Server;
* PostgreSQL Driver;
* Flyway;
* Spring Boot Actuator;
* Spring Boot Test;
* Spring Security Test;
* Testcontainers PostgreSQL;
* Springdoc OpenAPI.

Evitar introduzir dependências adicionais sem necessidade.

---

# 7. Dependências deliberadamente evitadas

## Lombok

Não será utilizado inicialmente.

Motivos:

* o Codex pode gerar o boilerplate necessário;
* reduz dependências externas;
* deixa o código mais explícito para a avaliação;
* facilita explicar o funcionamento das classes.

## MapStruct

Não será utilizado inicialmente.

Como o projeto possui poucos modelos, os mapeamentos serão pequenos e explícitos.

MapStruct só deverá ser introduzido se surgir complexidade real de mapeamento.

---

# 8. Organização dos pacotes

Será utilizada organização principalmente **por funcionalidade/domínio**, evitando uma aplicação inteira separada apenas em grandes pastas globais de controller/service/repository.

Estrutura prevista:

```text
com.vortex.marketplace
│
├── auth/
│   ├── controller/
│   ├── dto/
│   └── service/
│
├── user/
│   ├── domain/
│   └── repository/
│
├── listing/
│   ├── controller/
│   ├── domain/
│   ├── dto/
│   ├── repository/
│   └── service/
│
├── security/
│
├── config/
│
└── shared/
    └── exception/
```

A estrutura pode receber pequenos ajustes se o código real justificar, mas não deverá ser substituída por outra arquitetura sem decisão explícita.

---

# 9. Fluxo principal do Backend

O fluxo padrão será:

```text
HTTP Request
     ↓
Controller
     ↓
Service
     ↓
Repository
     ↓
PostgreSQL
```

Responsabilidades:

## Controller

Responsável por:

* receber requisições HTTP;
* validar entrada através de DTOs;
* chamar o Service adequado;
* retornar a resposta HTTP.

Não deverá conter regra de negócio relevante.

## Service

Responsável por:

* regras de negócio;
* autorização relacionada ao domínio;
* orquestração das operações;
* criação e alteração das entidades;
* tratamento de situações esperadas do domínio.

## Repository

Responsável pelo acesso aos dados.

Será baseado principalmente em Spring Data JPA.

## DTO

Entidades JPA não deverão ser retornadas diretamente pela API.

A API utilizará DTOs próprios para:

* requests;
* responses.

Sempre que adequado, DTOs poderão ser implementados utilizando Java `record`.

---

# 10. Modelo de domínio

A versão inicial possuirá apenas duas entidades persistentes principais:

```text
User
Listing
```

Relacionamento:

```text
User 1 ─────────── N Listing
```

Um usuário pode possuir vários anúncios.

Um anúncio possui exatamente um proprietário.

Não será criada inicialmente uma coleção bidirecional `User.listings`.

A relação necessária será mantida do lado de `Listing` para `User`.

Isso reduz complexidade de serialização e manipulação das entidades.

---

# 11. Entidade User

Tabela:

```text
users
```

Campos planejados:

```text
id
name
email
passwordHash
createdAt
```

Tipos conceituais:

| Campo        | Tipo    |
| ------------ | ------- |
| id           | Long    |
| name         | String  |
| email        | String  |
| passwordHash | String  |
| createdAt    | Instant |

---

# 12. Regras de User

## ID

Utilizar `Long` com geração automática pelo banco.

UUID não será utilizado neste projeto inicialmente.

Motivo:

* menor complexidade;
* suficiente para o escopo;
* fácil compreensão durante a avaliação.

A previsibilidade de IDs não deverá ser utilizada como mecanismo de autorização.

---

## Nome

Regras planejadas:

* obrigatório;
* mínimo de 2 caracteres;
* máximo de 100 caracteres.

---

## Email

Regras:

* obrigatório;
* formato válido;
* máximo de 254 caracteres;
* único no sistema.

Antes da persistência, o email deverá ser normalizado:

```text
trim
lowercase
```

Exemplo:

```text
Paulo@Email.com
```

torna-se:

```text
paulo@email.com
```

O banco deverá possuir restrição `UNIQUE` para email.

---

## Senha

A senha recebida no cadastro nunca será persistida diretamente.

Será armazenado somente seu hash.

Algoritmo:

```text
BCrypt
```

Regras iniciais:

* mínimo de 8 caracteres;
* máximo de 72 caracteres.

Passwords nunca devem aparecer em logs ou responses.

---

# 13. Entidade Listing

`Listing` representa um anúncio publicado no marketplace.

Tabela:

```text
listings
```

Campos:

```text
id
title
description
category
type
price
imageUrl
createdAt
owner
```

Tipos conceituais:

| Campo       | Tipo                |
| ----------- | ------------------- |
| id          | Long                |
| title       | String              |
| description | String              |
| category    | ListingCategory     |
| type        | ListingType         |
| price       | BigDecimal nullable |
| imageUrl    | String              |
| createdAt   | Instant             |
| owner       | User                |

---

# 14. ListingType

Será utilizado um enum para representar se o anúncio é venda ou doação.

```java
SALE
DONATION
```

Não utilizar simultaneamente:

```text
boolean donation
+
price
```

como fonte primária da regra.

O enum evita estados semanticamente contraditórios.

---

# 15. Regra de preço

## Venda

Se:

```text
type = SALE
```

então:

```text
price != null
price > 0
```

## Doação

Se:

```text
type = DONATION
```

então:

```text
price = null
```

Essa regra deverá existir na camada de negócio.

Sempre que viável, também deverá existir uma constraint correspondente no banco de dados.

Dinheiro deverá utilizar:

```java
BigDecimal
```

Nunca utilizar:

```java
double
float
```

para preço.

Precisão inicialmente prevista no PostgreSQL:

```text
NUMERIC(10,2)
```

---

# 16. Categorias

Será utilizado inicialmente o enum:

```java
BOOKS
COMPUTING
ENGINEERING
ELECTRONICS
CLOTHING
OTHER
```

Os enums deverão ser persistidos no banco como texto e não como posição numérica.

Ou seja:

```text
BOOKS
```

e não:

```text
0
```

Isso evita corrupção semântica caso a ordem dos enums seja alterada posteriormente.

---

# 17. Regras de Listing

## title

* obrigatório;
* mínimo de 3 caracteres;
* máximo de 120 caracteres.

## description

* obrigatório;
* mínimo de 10 caracteres;
* máximo de 2000 caracteres.

## category

* obrigatória;
* deve ser um valor válido de `ListingCategory`.

## type

* obrigatório;
* `SALE` ou `DONATION`.

## imageUrl

* obrigatória;
* máximo de 2048 caracteres;
* deve possuir formato válido de URL.

## owner

* obrigatório;
* definido a partir do usuário autenticado.

O cliente não poderá escolher arbitrariamente o ID do proprietário no request de criação.

## createdAt

* gerado automaticamente;
* utilizar `Instant`.

---

# 18. Relação User → Listing

No JPA:

```text
Listing
  ManyToOne
     ↓
   User
```

A relação deverá utilizar carregamento `LAZY`.

Não utilizar serialização direta da entidade para JSON.

DTOs deverão controlar os dados retornados.

---

# 19. Autenticação

Será implementada autenticação utilizando:

```text
Spring Security
+
JWT Bearer Token
```

Fluxo:

```text
Cadastro
   ↓
Login
   ↓
JWT
   ↓
Authorization: Bearer <token>
   ↓
Endpoints protegidos
```

---

# 20. Estratégia JWT

A primeira versão utilizará somente:

```text
Access Token
```

Não será criado Refresh Token inicialmente.

Motivo:

* o edital solicita apenas autenticação básica;
* refresh tokens aumentariam a superfície de implementação;
* não agregam valor suficiente para o desafio atual.

Tempo inicial previsto para o access token:

```text
60 minutos
```

A duração deverá ser configurável.

O token deverá possuir no mínimo informações suficientes para identificar o usuário autenticado.

A chave usada para assinatura nunca deverá ser escrita diretamente no código-fonte ou enviada ao Git.

Deverá ser fornecida através de configuração externa / variável de ambiente.

---

# 21. Autorização

Rotas públicas previstas:

```text
registro
login
listagem pública de anúncios
detalhes públicos de anúncio
documentação da API, conforme configuração
health check
```

Rotas protegidas previstas:

```text
criar anúncio
listar meus anúncios
excluir anúncio próprio
```

Regra fundamental:

> um usuário jamais poderá excluir o anúncio de outro usuário.

A autorização deve ser verificada no Backend.

Nunca confiar apenas em esconder botões no Frontend.

---

# 22. Sessão e segurança HTTP

A API será stateless.

Não utilizar sessão HTTP para armazenar autenticação.

A autenticação será enviada através de Bearer Token.

CSRF deverá ser configurado de forma adequada para uma API REST stateless baseada em Bearer Token.

CORS deverá aceitar apenas origens explicitamente configuradas.

As origens permitidas deverão poder ser definidas através de configuração externa.

---

# 23. Persistência

Banco principal:

```text
PostgreSQL
```

Não utilizar H2 como banco principal.

Ambiente local deverá poder iniciar PostgreSQL através de Docker Compose.

---

# 24. Docker

O monorepo possuirá um:

```text
compose.yaml
```

para facilitar o ambiente local.

Inicialmente ele será responsável por iniciar PostgreSQL.

Backend e Frontend poderão ser executados diretamente durante desenvolvimento.

A containerização completa da aplicação poderá ser adicionada posteriormente para deploy caso seja útil.

---

# 25. Flyway

Flyway será responsável por controlar o schema do banco.

Não utilizar:

```text
spring.jpa.hibernate.ddl-auto=update
```

como mecanismo de criação ou evolução do banco.

Em ambientes normais, Hibernate deverá validar o schema existente.

Estratégia planejada:

```text
ddl-auto=validate
```

Migrações iniciais previstas:

```text
V1__create_users_table.sql
V2__create_listings_table.sql
V3__create_indexes.sql
```

Migrações já aplicadas não deverão ser modificadas retroativamente.

Alterações posteriores deverão criar novas migrations.

---

# 26. Constraints e índices

O banco deverá possuir, sempre que apropriado, garantias além das validações Java.

Planejado inicialmente:

## users

```text
PRIMARY KEY id
UNIQUE email
NOT NULL name
NOT NULL email
NOT NULL password_hash
NOT NULL created_at
```

## listings

```text
PRIMARY KEY id
NOT NULL title
NOT NULL description
NOT NULL category
NOT NULL type
NOT NULL image_url
NOT NULL created_at
NOT NULL owner_id
FOREIGN KEY owner_id → users.id
```

Também deverá existir uma constraint garantindo a coerência entre:

```text
SALE → price > 0
DONATION → price IS NULL
```

Índices inicialmente úteis:

```text
users.email
listings.category
listings.owner_id
listings.created_at
```

Não criar índices indiscriminadamente.

---

# 27. Ordenação e paginação

Listagens de anúncios deverão ser preparadas para paginação.

Ordenação padrão prevista:

```text
createdAt DESC
```

Ou seja, anúncios mais recentes primeiro.

A forma HTTP definitiva será estabelecida em `API_CONTRACT.md`.

---

# 28. Filtros

Filtro obrigatório:

```text
category
```

Filtros adicionais simples poderão ser suportados caso não adicionem complexidade significativa.

Exemplos considerados:

```text
type
search
```

Esses filtros adicionais somente serão formalizados quando definidos no `API_CONTRACT.md`.

---

# 29. Tratamento de erros

A API deverá possuir tratamento centralizado de exceções.

Utilizar:

```java
@RestControllerAdvice
```

As respostas de erro deverão seguir formato consistente.

Preferencialmente utilizar o padrão:

```text
Problem Details for HTTP APIs
```

com campos adicionais quando necessário para representar erros de validação.

Não retornar:

* stack trace;
* detalhes internos do banco;
* senha;
* JWT;
* informações sensíveis.

---

# 30. Exemplos de erros que deverão possuir tratamento

```text
400 — request inválido
401 — autenticação ausente ou inválida
403 — usuário sem autorização
404 — recurso inexistente
409 — conflito, como email já cadastrado
500 — erro interno inesperado
```

A definição exata das respostas será feita em `API_CONTRACT.md`.

---

# 31. Documentação da API

Será utilizado:

```text
OpenAPI
+
Swagger UI
```

Biblioteca prevista:

```text
springdoc-openapi
```

A documentação deverá permitir visualizar e compreender:

* endpoints;
* parâmetros;
* bodies;
* responses;
* autenticação;
* principais erros.

O objetivo não é adicionar dezenas de annotations redundantes.

A documentação deve ser clara e útil.

---

# 32. Health Check

Será utilizado:

```text
Spring Boot Actuator
```

para disponibilizar um health check simples.

Em produção, somente endpoints de Actuator necessários deverão ser expostos publicamente.

O principal será:

```text
/actuator/health
```

Isso facilitará deploy e diagnóstico da aplicação.

---

# 33. Configuração

Utilizar configurações externas para dados que variam entre ambientes.

Nunca versionar:

* senha do banco de produção;
* JWT secret;
* tokens;
* credenciais;
* secrets de serviços externos.

Estrutura prevista:

```text
application.yml
application-dev.yml
application-test.yml
application-prod.yml
```

Variáveis sensíveis deverão vir do ambiente.

Deverá existir posteriormente um:

```text
.env.example
```

ou documentação equivalente contendo apenas nomes e exemplos seguros das configurações necessárias.

---

# 34. Ambientes

## dev

Desenvolvimento local.

PostgreSQL via Docker Compose.

## test

Execução de testes automatizados.

## prod

Deploy real.

Configurações e segredos através de environment variables.

---

# 35. Testes

Testes são parte da implementação, não uma etapa opcional feita apenas no final.

Utilizar:

* JUnit;
* Mockito;
* Spring Boot Test;
* Spring Security Test;
* MockMvc;
* Testcontainers para integração com PostgreSQL.

---

# 36. Estratégia de testes

## Testes unitários

Priorizar regras de negócio importantes.

Exemplos:

```text
criação de venda válida
criação de doação válida
venda sem preço
doação com preço
exclusão por proprietário
tentativa de exclusão por outro usuário
```

## Testes Web/API

Validar:

```text
status HTTP
validação de requests
autenticação
autorização
serialização de responses
```

## Testes de integração

Utilizar PostgreSQL real através de Testcontainers nas situações em que integração com banco ou migrations seja relevante.

Não utilizar mocks onde o objetivo do teste seja verificar integração real.

---

# 37. Regras para testes

O Codex não poderá:

* apagar um teste porque ele falhou;
* desativar um teste para obter build verde;
* alterar uma regra de negócio para satisfazer um teste incorreto;
* declarar testes como aprovados sem executá-los.

Quando um teste falhar, deverá ser determinado primeiro se o problema está:

1. na implementação;
2. no teste;
3. na especificação.

---

# 38. Logging

Utilizar o sistema padrão de logging integrado ao Spring.

Logs deverão ser úteis para diagnóstico sem exposição de dados sensíveis.

Nunca registrar:

```text
password
passwordHash
JWT completo
JWT secret
credenciais de banco
```

---

# 39. Frontend — arquitetura planejada

O Frontend será implementado posteriormente.

Stack planejada:

```text
React
TypeScript
Vite
```

Será uma SPA responsiva com suporte a PWA.

Bibliotecas adicionais serão escolhidas apenas quando chegarmos ao planejamento específico do Frontend.

Não definir antecipadamente bibliotecas desnecessárias.

---

# 40. PWA

Requisitos mínimos:

```text
Web App Manifest
Service Worker
instalabilidade
responsividade
```

Cache offline avançado será implementado somente depois que as funcionalidades essenciais estiverem estáveis.

A estratégia específica será documentada no planejamento do Frontend.

---

# 41. Deploy

Backend e Frontend deverão possuir deploy público antes da entrega, desde que tecnicamente possível dentro do prazo.

A escolha do provedor será feita posteriormente.

Critérios:

* facilidade de deploy;
* baixo ou nenhum custo;
* suporte às tecnologias utilizadas;
* configuração de HTTPS;
* integração simples com variáveis de ambiente.

Nenhuma decisão arquitetural importante deverá depender prematuramente de um provedor específico.

---

# 42. API

Todas as rotas da aplicação deverão ser versionadas.

Prefixo planejado:

```text
/api/v1
```

Exemplo conceitual:

```text
/api/v1/auth
/api/v1/listings
```

Os endpoints definitivos serão especificados exclusivamente em:

```text
docs/API_CONTRACT.md
```

Este documento não deve ser utilizado como contrato HTTP definitivo.

---

# 43. Convenções de nomenclatura

Código Java:

```text
inglês
```

Exemplos:

```java
User
Listing
ListingType
ListingCategory
AuthService
ListingService
```

Documentação e comentários explicativos:

```text
português
```

Nomes técnicos consagrados poderão permanecer em inglês.

Banco:

```text
snake_case
```

Exemplos:

```text
password_hash
created_at
owner_id
```

Java:

```text
camelCase
```

Exemplos:

```text
passwordHash
createdAt
ownerId
```

---

# 44. Comentários no código

Não adicionar comentários explicando código óbvio.

Evitar:

```java
// pega o usuário
User user = ...
```

Comentários devem existir quando explicarem:

* decisão não evidente;
* regra de negócio;
* workaround;
* comportamento importante;
* motivo de determinada implementação.

---

# 45. Regra de simplicidade

Quando houver duas soluções igualmente corretas, preferir:

```text
a solução mais simples de compreender,
testar,
manter
e explicar.
```

Não utilizar padrões arquiteturais apenas para aumentar a aparência de complexidade do projeto.

---

# 46. Mudanças arquiteturais

O Codex não está autorizado a alterar unilateralmente decisões deste documento.

Caso uma implementação revele problema com uma decisão arquitetural, o Codex deverá:

1. identificar o problema;
2. explicar por que a arquitetura atual é inadequada;
3. propor uma alternativa;
4. aguardar alteração explícita deste documento antes de aplicar mudança estrutural relevante.

Pequenos detalhes internos que não contrariem este documento podem ser decididos durante a implementação.

---

# 47. Fonte de verdade

Ordem de autoridade das especificações:

```text
1. Edital do desafio
2. docs/REQUIREMENTS.md
3. docs/ARCHITECTURE.md
4. docs/API_CONTRACT.md
5. ExecPlan atual
6. implementação existente
```

Se houver contradição entre documentos, a implementação não deverá simplesmente escolher uma interpretação.

A inconsistência deverá ser apontada antes de uma alteração significativa.

---

# 48. Estado atual

Neste estágio:

* arquitetura definida;
* implementação ainda não iniciada;
* endpoints HTTP definitivos ainda não definidos;
* contrato da API ainda não definido;
* Frontend ainda não inicializado.

O próximo documento a ser definido será:

```text
docs/API_CONTRACT.md
```
