# Contrato da API — Marketplace de Economia Circular Vortex

## 1. Objetivo

Este documento define o contrato HTTP oficial entre Backend e Frontend.

Ele especifica:

* endpoints;
* métodos HTTP;
* autenticação;
* parâmetros;
* requests;
* responses;
* códigos HTTP;
* paginação;
* filtros;
* formato de erros.

O Backend e o Frontend devem respeitar este contrato.

Mudanças neste documento devem ser feitas explicitamente antes de alterações incompatíveis na implementação.

---

# 2. Convenções gerais

Base path da API:

```text
/api/v1
```

Formato principal:

```text
application/json
```

Erros deverão utilizar preferencialmente:

```text
application/problem+json
```

Todas as datas serão retornadas em ISO 8601 e representarão instantes UTC.

Exemplo:

```text
2026-08-10T22:15:30Z
```

---

# 3. Autenticação

Endpoints protegidos utilizarão:

```http
Authorization: Bearer <access-token>
```

Exemplo:

```http
Authorization: Bearer eyJhbGciOi...
```

A API será stateless.

Não será utilizada sessão HTTP para autenticação.

---

# 4. Recursos principais

A API possuirá três grupos principais:

```text
/api/v1/auth
/api/v1/users
/api/v1/listings
```

---

# 5. Resumo dos endpoints

## Autenticação

```http
POST /api/v1/auth/register
POST /api/v1/auth/login
```

## Usuário

```http
GET /api/v1/users/me
```

## Anúncios

```http
GET    /api/v1/listings
GET    /api/v1/listings/{id}
POST   /api/v1/listings
GET    /api/v1/listings/me
DELETE /api/v1/listings/{id}
```

---

# 6. Endpoints públicos

Não exigem JWT:

```text
POST /auth/register
POST /auth/login

GET /listings
GET /listings/{id}
```

Também serão públicos, conforme configuração:

```text
GET /actuator/health
documentação OpenAPI / Swagger
```

---

# 7. Endpoints protegidos

Exigem JWT válido:

```text
GET /users/me
POST /listings
GET /listings/me
DELETE /listings/{id}
```

---

# 8. Modelo público de usuário

Dados públicos de um proprietário de anúncio:

```json
{
  "id": 7,
  "name": "Paulo Rocha"
}
```

O email do proprietário não será exposto publicamente através dos anúncios.

---

# 9. Modelo do usuário autenticado

Quando o próprio usuário consulta seus dados:

```json
{
  "id": 7,
  "name": "Paulo Rocha",
  "email": "paulo@example.com",
  "createdAt": "2026-08-10T20:30:00Z"
}
```

Nunca retornar:

```text
password
passwordHash
```

---

# 10. Modelo de anúncio

Exemplo de response:

```json
{
  "id": 42,
  "title": "Clean Code",
  "description": "Livro usado em ótimo estado.",
  "category": "BOOKS",
  "type": "SALE",
  "price": 70.00,
  "imageUrl": "https://example.com/clean-code.jpg",
  "createdAt": "2026-08-10T21:00:00Z",
  "owner": {
    "id": 7,
    "name": "Paulo Rocha"
  }
}
```

Para doação:

```json
{
  "id": 43,
  "title": "Jaleco branco",
  "description": "Jaleco usado durante dois semestres.",
  "category": "CLOTHING",
  "type": "DONATION",
  "price": null,
  "imageUrl": "https://example.com/jaleco.jpg",
  "createdAt": "2026-08-10T21:05:00Z",
  "owner": {
    "id": 7,
    "name": "Paulo Rocha"
  }
}
```

---

# 11. Categorias válidas

Valores aceitos:

```text
BOOKS
COMPUTING
ENGINEERING
ELECTRONICS
CLOTHING
OTHER
```

O Backend deve rejeitar valores diferentes.

---

# 12. Tipos de anúncio

Valores aceitos:

```text
SALE
DONATION
```

---

# 13. Cadastro de usuário

## Endpoint

```http
POST /api/v1/auth/register
```

## Autenticação

Não requerida.

## Request

```json
{
  "name": "Paulo Rocha",
  "email": "paulo@example.com",
  "password": "minhaSenha123"
}
```

## Validações

### name

```text
obrigatório
2 a 100 caracteres
```

### email

```text
obrigatório
formato válido
máximo de 254 caracteres
```

O Backend deverá normalizar:

```text
trim
lowercase
```

antes de realizar comparação e persistência.

### password

```text
obrigatória
8 a 72 caracteres
```

---

# 14. Response de cadastro

Cadastro realizado:

```http
201 Created
```

Response:

```json
{
  "accessToken": "eyJhbGciOi...",
  "tokenType": "Bearer",
  "expiresIn": 3600,
  "user": {
    "id": 7,
    "name": "Paulo Rocha",
    "email": "paulo@example.com",
    "createdAt": "2026-08-10T20:30:00Z"
  }
}
```

O cadastro realizará autenticação automática do novo usuário.

---

# 15. Possíveis erros de cadastro

Request inválido:

```http
400 Bad Request
```

Email já cadastrado:

```http
409 Conflict
```

---

# 16. Login

## Endpoint

```http
POST /api/v1/auth/login
```

## Request

```json
{
  "email": "paulo@example.com",
  "password": "minhaSenha123"
}
```

O email deverá ser normalizado antes da autenticação.

---

# 17. Response de login

Login válido:

```http
200 OK
```

```json
{
  "accessToken": "eyJhbGciOi...",
  "tokenType": "Bearer",
  "expiresIn": 3600,
  "user": {
    "id": 7,
    "name": "Paulo Rocha",
    "email": "paulo@example.com",
    "createdAt": "2026-08-10T20:30:00Z"
  }
}
```

Credenciais inválidas:

```http
401 Unauthorized
```

A resposta não deverá indicar se:

* o email não existe;
* a senha está incorreta.

Mensagem genérica:

```text
Email ou senha inválidos.
```

---

# 18. Consultar usuário autenticado

## Endpoint

```http
GET /api/v1/users/me
```

## Autenticação

Obrigatória.

## Response

```http
200 OK
```

```json
{
  "id": 7,
  "name": "Paulo Rocha",
  "email": "paulo@example.com",
  "createdAt": "2026-08-10T20:30:00Z"
}
```

JWT ausente ou inválido:

```http
401 Unauthorized
```

---

# 19. Criar anúncio

## Endpoint

```http
POST /api/v1/listings
```

## Autenticação

Obrigatória.

## Request

Venda:

```json
{
  "title": "Clean Code",
  "description": "Livro usado em ótimo estado.",
  "category": "BOOKS",
  "type": "SALE",
  "price": 70.00,
  "imageUrl": "https://example.com/clean-code.jpg"
}
```

Doação:

```json
{
  "title": "Jaleco branco",
  "description": "Jaleco usado durante dois semestres.",
  "category": "CLOTHING",
  "type": "DONATION",
  "price": null,
  "imageUrl": "https://example.com/jaleco.jpg"
}
```

O request NÃO deverá possuir:

```text
id
ownerId
owner
createdAt
```

Esses valores serão definidos pelo Backend.

---

# 20. Validação de anúncio

## title

```text
obrigatório
3 a 120 caracteres
```

## description

```text
obrigatória
10 a 2000 caracteres
```

## category

```text
obrigatória
valor existente em ListingCategory
```

## type

```text
obrigatório
SALE ou DONATION
```

## imageUrl

```text
obrigatória
URL válida
máximo de 2048 caracteres
```

---

# 21. Regra SALE

Se:

```text
type = SALE
```

então:

```text
price != null
price > 0
```

Exemplo inválido:

```json
{
  "type": "SALE",
  "price": null
}
```

---

# 22. Regra DONATION

Se:

```text
type = DONATION
```

então obrigatoriamente:

```text
price = null
```

Exemplo inválido:

```json
{
  "type": "DONATION",
  "price": 20.00
}
```

O Backend não deverá simplesmente ignorar o preço.

Deverá rejeitar a combinação inconsistente.

---

# 23. Response de criação

```http
201 Created
```

Header:

```http
Location: /api/v1/listings/42
```

Body:

```json
{
  "id": 42,
  "title": "Clean Code",
  "description": "Livro usado em ótimo estado.",
  "category": "BOOKS",
  "type": "SALE",
  "price": 70.00,
  "imageUrl": "https://example.com/clean-code.jpg",
  "createdAt": "2026-08-10T21:00:00Z",
  "owner": {
    "id": 7,
    "name": "Paulo Rocha"
  }
}
```

---

# 24. Listar anúncios

## Endpoint

```http
GET /api/v1/listings
```

## Autenticação

Não requerida.

## Ordenação padrão

```text
createdAt DESC
```

Anúncios mais recentes aparecem primeiro.

---

# 25. Paginação

Parâmetros:

```text
page
size
```

Exemplo:

```http
GET /api/v1/listings?page=0&size=12
```

Valores padrão:

```text
page = 0
size = 12
```

Limite máximo:

```text
size = 50
```

Requests com `size` superior ao limite deverão ser rejeitados ou normalizados conforme implementação definida, desde que o comportamento permaneça consistente e documentado.

Preferência do projeto:

```text
rejeitar com 400
```

---

# 26. Response paginada

A API NÃO deverá expor diretamente a estrutura interna de `Page` do Spring.

Utilizar response próprio:

```json
{
  "content": [
    {
      "id": 42,
      "title": "Clean Code",
      "description": "Livro usado em ótimo estado.",
      "category": "BOOKS",
      "type": "SALE",
      "price": 70.00,
      "imageUrl": "https://example.com/clean-code.jpg",
      "createdAt": "2026-08-10T21:00:00Z",
      "owner": {
        "id": 7,
        "name": "Paulo Rocha"
      }
    }
  ],
  "page": 0,
  "size": 12,
  "totalElements": 1,
  "totalPages": 1,
  "first": true,
  "last": true
}
```

---

# 27. Filtro por categoria

Exemplo:

```http
GET /api/v1/listings?category=BOOKS
```

Resultado:

somente anúncios da categoria `BOOKS`.

Categoria inexistente:

```http
400 Bad Request
```

---

# 28. Filtro por tipo

Exemplo:

```http
GET /api/v1/listings?type=DONATION
```

Resultado:

somente anúncios de doação.

Outro exemplo:

```http
GET /api/v1/listings?type=SALE
```

---

# 29. Busca textual

Parâmetro:

```text
search
```

Exemplo:

```http
GET /api/v1/listings?search=java
```

A busca deverá considerar inicialmente:

```text
title
description
```

A busca deverá ser case-insensitive.

Não será utilizado mecanismo de full-text search avançado nesta versão.

---

# 30. Combinação de filtros

Filtros poderão ser combinados.

Exemplo:

```http
GET /api/v1/listings?category=BOOKS&type=SALE&search=java&page=0&size=12
```

Todos os filtros fornecidos deverão ser aplicados simultaneamente.

---

# 31. Consultar anúncio específico

## Endpoint

```http
GET /api/v1/listings/{id}
```

Exemplo:

```http
GET /api/v1/listings/42
```

## Autenticação

Não requerida.

## Sucesso

```http
200 OK
```

```json
{
  "id": 42,
  "title": "Clean Code",
  "description": "Livro usado em ótimo estado.",
  "category": "BOOKS",
  "type": "SALE",
  "price": 70.00,
  "imageUrl": "https://example.com/clean-code.jpg",
  "createdAt": "2026-08-10T21:00:00Z",
  "owner": {
    "id": 7,
    "name": "Paulo Rocha"
  }
}
```

Anúncio inexistente:

```http
404 Not Found
```

---

# 32. Listar meus anúncios

## Endpoint

```http
GET /api/v1/listings/me
```

## Autenticação

Obrigatória.

A identidade do proprietário deverá ser obtida exclusivamente através do usuário autenticado.

Não aceitar:

```text
userId
ownerId
```

como parâmetro para determinar o usuário desta rota.

---

# 33. Paginação de meus anúncios

Aceitar:

```text
page
size
```

Exemplo:

```http
GET /api/v1/listings/me?page=0&size=12
```

Ordenação:

```text
createdAt DESC
```

Response utilizará o mesmo formato paginado de `GET /listings`.

---

# 34. Excluir anúncio

## Endpoint

```http
DELETE /api/v1/listings/{id}
```

## Autenticação

Obrigatória.

## Regra de autorização

Somente o proprietário poderá excluir o anúncio.

Fluxo conceitual:

```text
usuário autenticado
        ↓
consultar Listing
        ↓
Listing.owner == usuário autenticado?
        ↓
     SIM → excluir
     NÃO → 403
```

---

# 35. Response de exclusão

Sucesso:

```http
204 No Content
```

Sem body.

---

# 36. Erros de exclusão

Anúncio não encontrado:

```http
404 Not Found
```

Usuário autenticado não é proprietário:

```http
403 Forbidden
```

JWT ausente ou inválido:

```http
401 Unauthorized
```

---

# 37. Atualização de anúncio

Nesta versão do projeto NÃO será criado:

```http
PUT /listings/{id}
PATCH /listings/{id}
```

Motivo:

o edital exige criar, listar, filtrar e deletar anúncios, mas não exige edição.

A funcionalidade poderá ser adicionada posteriormente somente se:

* todos os requisitos principais estiverem concluídos;
* testes estiverem estáveis;
* deploy estiver funcional;
* houver tempo disponível.

Não implementar automaticamente.

---

# 38. Formato padrão de erro

A API deverá utilizar Problem Details.

Exemplo:

```json
{
  "type": "about:blank",
  "title": "Recurso não encontrado",
  "status": 404,
  "detail": "O anúncio solicitado não foi encontrado.",
  "instance": "/api/v1/listings/999",
  "code": "LISTING_NOT_FOUND"
}
```

---

# 39. Erros de validação

Exemplo:

```http
400 Bad Request
```

```json
{
  "type": "about:blank",
  "title": "Dados inválidos",
  "status": 400,
  "detail": "Um ou mais campos possuem valores inválidos.",
  "instance": "/api/v1/listings",
  "code": "VALIDATION_ERROR",
  "errors": [
    {
      "field": "title",
      "message": "O título deve possuir entre 3 e 120 caracteres."
    },
    {
      "field": "price",
      "message": "O preço é obrigatório para anúncios do tipo SALE."
    }
  ]
}
```

---

# 40. Códigos de erro da aplicação

Códigos inicialmente previstos:

```text
VALIDATION_ERROR
INVALID_CREDENTIALS
EMAIL_ALREADY_EXISTS
UNAUTHORIZED
ACCESS_DENIED
USER_NOT_FOUND
LISTING_NOT_FOUND
INVALID_LISTING_PRICE
INTERNAL_ERROR
```

Novos códigos poderão ser adicionados caso representem situações reais do domínio.

Não criar códigos diferentes para o mesmo problema sem necessidade.

---

# 41. 400 Bad Request

Utilizado para:

* validações de campos;
* enum inválido;
* parâmetros inválidos;
* paginação inválida;
* combinação inconsistente de tipo e preço;
* JSON malformado.

---

# 42. 401 Unauthorized

Utilizado quando:

* JWT não foi enviado;
* JWT é inválido;
* JWT expirou;
* credenciais de login estão incorretas.

---

# 43. 403 Forbidden

Utilizado quando:

* usuário está autenticado;
* mas não possui autorização para realizar a operação.

Exemplo:

```text
tentar excluir anúncio pertencente a outro usuário
```

---

# 44. 404 Not Found

Utilizado quando o recurso solicitado não existe.

Exemplo:

```text
LISTING_NOT_FOUND
```

---

# 45. 409 Conflict

Utilizado quando a operação conflita com o estado atual dos dados.

Exemplo:

```text
EMAIL_ALREADY_EXISTS
```

---

# 46. 500 Internal Server Error

Erros inesperados deverão retornar uma mensagem genérica.

Exemplo:

```json
{
  "type": "about:blank",
  "title": "Erro interno",
  "status": 500,
  "detail": "Ocorreu um erro inesperado.",
  "instance": "/api/v1/listings",
  "code": "INTERNAL_ERROR"
}
```

Nunca retornar ao cliente:

* stack trace;
* SQL;
* nome de tabelas;
* credenciais;
* secrets;
* detalhes sensíveis da infraestrutura.

---

# 47. Campos desconhecidos em requests

Preferência do projeto:

requests contendo propriedades JSON desconhecidas deverão ser rejeitados.

Exemplo:

```json
{
  "title": "Livro",
  "admin": true
}
```

Se `admin` não fizer parte do DTO esperado, o request deverá falhar.

Objetivo:

* detectar erros do Frontend rapidamente;
* evitar contratos silenciosamente divergentes;
* impedir que campos enviados por engano sejam ignorados sem percepção.

---

# 48. IDs inválidos

Exemplo:

```http
GET /api/v1/listings/abc
```

Deverá retornar:

```http
400 Bad Request
```

ID numericamente válido, mas inexistente:

```http
GET /api/v1/listings/999999
```

Deverá retornar:

```http
404 Not Found
```

---

# 49. Valores monetários

JSON deverá utilizar número:

```json
{
  "price": 70.00
}
```

e não string:

```json
{
  "price": "70.00"
}
```

Internamente o Backend utilizará `BigDecimal`.

---

# 50. Segurança de ownership

Nenhuma operação protegida deverá confiar em um `ownerId` enviado pelo Frontend.

Exemplo proibido:

```json
{
  "ownerId": 7,
  "title": "Clean Code"
}
```

O proprietário deverá ser obtido a partir do JWT autenticado.

---

# 51. Compatibilidade do Frontend

O Frontend deverá utilizar este documento para definir seus tipos TypeScript.

Exemplo conceitual:

```text
Listing
User
AuthResponse
PageResponse<Listing>
ProblemResponse
```

O Frontend não deverá criar formatos incompatíveis esperando que o Backend se adapte silenciosamente.

---

# 52. Health Check

Endpoint:

```http
GET /actuator/health
```

Resultado saudável esperado:

```http
200 OK
```

Exemplo conceitual:

```json
{
  "status": "UP"
}
```

Esse endpoint não utiliza o prefixo `/api/v1`, pois pertence ao Spring Boot Actuator e não ao domínio da API.

---

# 53. OpenAPI

A documentação OpenAPI deverá refletir este contrato.

Endpoints de documentação deverão estar disponíveis em desenvolvimento e, caso seja considerado seguro, em produção.

Rotas padrão inicialmente esperadas:

```text
/v3/api-docs
/swagger-ui/index.html
```

A implementação poderá configurar caminhos equivalentes sem alterar o contrato funcional da API.

---

# 54. Versionamento

O contrato atual utiliza:

```text
v1
```

Não criar:

```text
v2
```

durante este desafio.

Alterações compatíveis deverão permanecer em `v1`.

---

# 55. O que NÃO faz parte da API atual

Não criar sem decisão explícita:

```text
PUT /listings
PATCH /listings
POST /payments
POST /checkout
POST /messages
POST /reviews
POST /favorites
POST /notifications
```

Também não criar:

* carrinho;
* pagamento;
* chat;
* sistema de entrega;
* avaliação de usuários;
* favoritos;
* notificações complexas.

---

# 56. Resumo final do contrato

## Público

```http
POST /api/v1/auth/register
POST /api/v1/auth/login

GET /api/v1/listings
GET /api/v1/listings/{id}
```

## Autenticado

```http
GET /api/v1/users/me

POST /api/v1/listings
GET /api/v1/listings/me
DELETE /api/v1/listings/{id}
```

## Infraestrutura

```http
GET /actuator/health

GET /v3/api-docs
GET /swagger-ui/index.html
```

---

# 57. Regra de alteração do contrato

O Codex não deverá:

* criar endpoint adicional sem necessidade;
* renomear endpoint;
* alterar request;
* alterar response;
* alterar status HTTP;
* adicionar campos públicos;
* remover campos;
* mudar regras de autenticação;

sem que a alteração seja explicitamente aprovada neste documento.

Caso a implementação revele uma inconsistência técnica, o Codex deverá apontá-la antes de alterar o contrato.

---

# 58. Estado atual

Neste momento:

```text
REQUIREMENTS.md     definido
ARCHITECTURE.md     definido
API_CONTRACT.md     definido
Backend             ainda não implementado
Frontend            ainda não implementado
```

A próxima etapa será definir as instruções permanentes do agente em:

```text
AGENTS.md
```

