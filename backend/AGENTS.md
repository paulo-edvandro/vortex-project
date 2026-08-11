# AGENTS.md — Backend

Este arquivo complementa o `AGENTS.md` localizado na raiz do repositório.

Todas as regras globais continuam válidas.

As instruções abaixo aplicam-se especificamente ao código localizado em `backend/`.

---

# 1. Stack oficial

O Backend utiliza:

* Java 21;
* Spring Boot 4.1.x;
* Maven;
* Spring MVC;
* Spring Data JPA;
* PostgreSQL;
* Flyway;
* Bean Validation;
* Spring Security quando a autenticação for implementada;
* JWT quando a autenticação for implementada;
* Actuator;
* JUnit;
* Mockito;
* MockMvc;
* Testcontainers.

Não substituir essas tecnologias sem alteração explícita em `docs/ARCHITECTURE.md`.

---

# 2. Build

Utilizar preferencialmente Maven Wrapper.

Windows:

```text
mvnw.cmd
```

Unix-like:

```text
./mvnw
```

Não depender da existência de uma instalação global específica do Maven quando o Wrapper estiver disponível.

---

# 3. Package raiz

Utilizar:

```text
com.vortex.marketplace
```

A organização deve seguir principalmente funcionalidades/domínios.

Estrutura conceitual prevista:

```text
com.vortex.marketplace
├── auth/
├── user/
├── listing/
├── security/
├── config/
└── shared/
```

Não criar packages vazios apenas para antecipar estrutura futura.

Um package deve surgir quando houver código real que pertença a ele.

---

# 4. Organização por funcionalidade

Preferir:

```text
listing/
├── controller/
├── domain/
├── dto/
├── repository/
└── service/
```

em vez de concentrar toda a aplicação em:

```text
controller/
service/
repository/
entity/
```

globais.

Manter elementos relacionados próximos uns dos outros.

---

# 5. Controllers

Controllers devem cuidar apenas da camada HTTP.

Responsabilidades apropriadas:

* receber parâmetros;
* receber DTOs;
* acionar validação;
* obter informações HTTP necessárias;
* chamar Services;
* retornar responses.

Controllers não devem conter regras de negócio relevantes.

Evitar lógica complexa dentro de métodos anotados com:

```java
@GetMapping
@PostMapping
@DeleteMapping
```

---

# 6. Services

Services concentram as regras de negócio e orquestração da aplicação.

Exemplos:

* validar coerência entre `ListingType` e `price`;
* garantir que somente o proprietário exclua um anúncio;
* obter o usuário autenticado;
* criar entidades;
* coordenar repositories.

Manter Services pequenos e focados.

Não criar interfaces de Service sem existir uma necessidade concreta.

Evitar o padrão automático:

```text
ListingService
ListingServiceImpl
```

quando existe apenas uma implementação e nenhuma razão real para a interface.

---

# 7. Repositories

Repositories devem tratar persistência.

Utilizar Spring Data JPA sempre que ele resolver a necessidade de maneira simples.

Não criar implementação manual de repository sem necessidade.

Queries devem permanecer legíveis.

Antes de criar uma query complexa, avaliar se existe uma solução mais simples através de:

* métodos derivados;
* JPQL;
* Specifications ou mecanismo equivalente, caso realmente necessário.

Não introduzir abstrações de acesso a dados apenas para aumentar camadas.

---

# 8. Entidades JPA

Entidades representam persistência e domínio interno.

Não retornar entidades JPA diretamente através dos Controllers.

A API deve utilizar DTOs.

Associações JPA devem evitar carregamento desnecessário.

Conforme definido na arquitetura:

```text
Listing → User
@ManyToOne
LAZY
```

Não criar relacionamento bidirecional `User.listings` sem necessidade real.

---

# 9. DTOs

Requests e responses HTTP devem possuir tipos próprios.

Preferir Java `record` quando o DTO for apenas um transportador imutável de dados e isso tornar o código mais simples.

Exemplo conceitual:

```java
public record CreateListingRequest(...) {}
```

Não reutilizar entidades JPA como DTOs.

Não expor propriedades internas acidentalmente.

---

# 10. Mapeamento

Os mapeamentos entre:

```text
Entity ↔ Response DTO
Request DTO → Entity
```

devem permanecer explícitos e simples.

Não introduzir MapStruct inicialmente.

Caso os mapeamentos aumentem significativamente de complexidade, apontar o problema antes de introduzir nova dependência.

---

# 11. Validação

Utilizar Bean Validation para regras estruturais simples.

Exemplos:

```java
@NotBlank
@NotNull
@Size
@Email
@Positive
```

Regras que dependam da combinação de vários campos ou do estado do sistema pertencem à camada de negócio.

Exemplo:

```text
SALE + price
DONATION + price
```

não deve depender exclusivamente de annotations simples.

---

# 12. Banco de dados

PostgreSQL é o banco oficial.

Não substituir por H2 para simplificar implementação.

Testes que precisem validar comportamento real de persistência deverão utilizar PostgreSQL através de Testcontainers quando apropriado.

---

# 13. Flyway

Flyway controla o schema.

Migrations:

```text
src/main/resources/db/migration/
```

Formato:

```text
V1__descricao.sql
V2__descricao.sql
V3__descricao.sql
```

Depois que uma migration for aplicada e fizer parte do histórico do projeto, não alterá-la retroativamente para representar mudanças novas.

Criar uma nova migration.

Não utilizar:

```text
ddl-auto=update
ddl-auto=create
ddl-auto=create-drop
```

como estratégia normal de gerenciamento do schema.

A configuração prevista é:

```text
ddl-auto=validate
```

quando aplicável.

---

# 14. Constraints

Não depender apenas de validações Java quando o próprio banco puder garantir uma regra importante.

Exemplos:

```text
NOT NULL
UNIQUE
FOREIGN KEY
CHECK
```

Devem ser utilizados quando definidos pela arquitetura e forem apropriados.

Validação de aplicação e integridade do banco são complementares.

---

# 15. Dinheiro

Valores monetários devem utilizar:

```java
BigDecimal
```

Nunca utilizar:

```java
double
float
```

para preços.

---

# 16. Datas

Datas de criação definidas pela arquitetura devem utilizar:

```java
Instant
```

Evitar misturar diferentes representações temporais sem necessidade.

---

# 17. Enums

Enums persistidos no banco devem utilizar representação textual.

Exemplo:

```java
@Enumerated(EnumType.STRING)
```

Não persistir enums pela posição ordinal.

---

# 18. Segurança

Quando Spring Security for introduzido:

* a API deve permanecer stateless;
* autenticação deve seguir o contrato definido;
* autorização deve ocorrer no Backend;
* o usuário autenticado deve ser fonte de identidade para operações protegidas.

Nunca confiar em um `ownerId` enviado pelo Frontend para determinar propriedade de recurso.

Nunca armazenar senha em texto puro.

Nunca registrar password, hash ou JWT completo em logs.

---

# 19. Configuração

Não inserir valores sensíveis diretamente em código ou arquivos versionados.

Utilizar configuration properties e environment variables quando apropriado.

Profiles previstos:

```text
dev
test
prod
```

Não espalhar chamadas diretas a environment variables por classes de negócio.

Centralizar configuração através dos mecanismos do Spring.

---

# 20. Exceções

Quando o tratamento global for implementado, utilizar uma estratégia centralizada.

Não adicionar `try/catch` repetitivo em Controllers para transformar toda exceção manualmente em resposta HTTP.

Exceções de domínio devem possuir nomes claros e representar situações reais.

Exemplos conceituais:

```text
ListingNotFoundException
EmailAlreadyExistsException
```

Não utilizar exceções genéricas como mecanismo normal de controle de fluxo.

---

# 21. API Contract

Antes de criar ou modificar Controller, consultar:

```text
docs/API_CONTRACT.md
```

Não inventar:

* endpoint;
* request;
* response;
* status;
* propriedade JSON;
* parâmetro;
* regra de autenticação.

Caso a implementação revele problema no contrato, apontar antes de modificá-lo.

---

# 22. Status HTTP

Utilizar os códigos definidos em `docs/API_CONTRACT.md`.

Não retornar `200 OK` indiscriminadamente para qualquer operação.

Exemplos já definidos pelo contrato incluem:

```text
201 Created
204 No Content
400 Bad Request
401 Unauthorized
403 Forbidden
404 Not Found
409 Conflict
500 Internal Server Error
```

---

# 23. Testes

Ao implementar comportamento de Backend, criar ou atualizar testes na mesma tarefa quando apropriado.

Não deixar sistematicamente todos os testes para o final do projeto.

Testar especialmente:

* regras de negócio;
* validações;
* autorização;
* persistência crítica;
* contrato HTTP;
* cenários de erro relevantes.

---

# 24. Tipos de testes

Utilizar o nível de teste adequado.

## Unitário

Para lógica isolada.

Normalmente:

```text
JUnit
Mockito
```

## Web

Para comportamento HTTP e segurança quando apropriado.

Normalmente:

```text
MockMvc
Spring Security Test
```

## Integração

Para comportamento que realmente precisa do contexto Spring, banco ou migrations.

Normalmente:

```text
Spring Boot Test
Testcontainers
PostgreSQL
```

Não iniciar contexto Spring completo para testar uma função simples que poderia ser testada unitariamente.

---

# 25. Testcontainers

Quando o teste tiver como objetivo validar integração com PostgreSQL, utilizar Testcontainers.

O teste não deve depender do desenvolvedor ter iniciado manualmente o PostgreSQL do `compose.yaml`.

O Compose serve ao ambiente local de desenvolvimento.

Testcontainers serve aos testes automatizados que exigem banco real.

---

# 26. Verificação durante implementação

Não acumular grande quantidade de código sem compilar.

Depois de alterações relevantes, utilizar verificações incrementais.

Exemplo:

Windows:

```text
mvnw.cmd test
```

ou, quando uma compilação rápida for suficiente:

```text
mvnw.cmd compile
```

Unix-like:

```text
./mvnw test
./mvnw compile
```

---

# 27. Verificação final de uma tarefa Backend

Quando aplicável, antes de concluir uma tarefa:

Windows:

```text
mvnw.cmd clean test
```

e, para mudanças relevantes:

```text
mvnw.cmd clean package
```

Unix-like:

```text
./mvnw clean test
./mvnw clean package
```

Não declarar sucesso se esses comandos não tiverem sido executados.

Caso alguma verificação não possa ser realizada, informar claramente.

---

# 28. Docker Compose

O `compose.yaml` da raiz é utilizado para infraestrutura local compartilhada.

Não adicionar novos serviços sem necessidade aprovada.

Não adicionar ferramentas auxiliares como:

```text
pgAdmin
Redis
RabbitMQ
```

apenas por conveniência ou aparência de sofisticação.

---

# 29. Performance

Não realizar otimizações prematuras.

Entretanto, evitar problemas evidentes como:

* N+1 queries conhecidas;
* carregamento EAGER desnecessário;
* carregar todos os registros quando existe paginação;
* consultas repetidas facilmente evitáveis.

Otimizar quando houver um problema concreto ou risco evidente.

---

# 30. Logging

Utilizar logging com propósito diagnóstico.

Evitar logs excessivos em fluxo normal.

Não utilizar:

```java
System.out.println()
```

como estratégia de logging da aplicação.

Não expor dados sensíveis.

---

# 31. Código gerado por IA

Código produzido pelo agente deve ser compreensível pelo desenvolvedor.

Não gerar abstrações complexas, reflection, metaprogramação ou padrões avançados sem necessidade.

Quando houver uma solução simples e uma solução sofisticada igualmente adequadas, utilizar a simples.

O projeto será apresentado e explicado pelo candidato durante a avaliação.

---

# 32. TODOs

Não deixar:

```text
TODO
FIXME
HACK
```

silenciosamente em funcionalidades consideradas concluídas.

Se algo realmente precisar permanecer pendente, informar explicitamente ao final da tarefa e, quando adequado, registrar no ExecPlan.

---

# 33. Warnings

Não ignorar warnings relevantes de compilação, banco ou configuração.

Investigar warnings que possam indicar:

* API obsoleta;
* configuração incorreta;
* migration problemática;
* comportamento inseguro.

Warnings irrelevantes de ferramentas externas podem ser documentados sem bloquear desnecessariamente a tarefa.

---

# 34. Alterações no pom.xml

Ao alterar `pom.xml`:

1. adicionar somente dependências necessárias;
2. evitar versões explícitas quando gerenciadas pelo Spring Boot;
3. verificar compatibilidade;
4. executar Maven depois da alteração.

Não acumular dependências "para usar futuramente".

---

# 35. Alterações em migrations

Ao criar migration:

1. revisar SQL;
2. verificar tipos PostgreSQL;
3. verificar constraints;
4. iniciar/testar contra PostgreSQL;
5. garantir compatibilidade com as entidades JPA correspondentes.

Não afirmar que migration funciona apenas porque o SQL parece correto.

---

# 36. Revisão antes de concluir

Antes de terminar uma tarefa relevante em `backend/`:

```text
git status
git diff
```

Verificar:

* arquivos modificados;
* alterações não relacionadas;
* configuração sensível;
* código temporário;
* imports não utilizados;
* inconsistências com os documentos do projeto.

---

# 37. Commits

Não realizar automaticamente:

```text
git commit
git push
```

O desenvolvedor fará a revisão antes desses passos.

Quando o estado atual representar um bom checkpoint, informar:

```text
Checkpoint adequado para commit.
```

---

# 38. Regra final do Backend

O objetivo não é construir o Backend mais sofisticado possível.

O objetivo é construir um Backend:

```text
correto
seguro
simples
testado
reproduzível
bem estruturado
fácil de explicar
```

Toda decisão técnica deve favorecer esses objetivos.
