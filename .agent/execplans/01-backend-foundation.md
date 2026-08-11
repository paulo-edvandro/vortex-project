# ExecPlan 01 — Fundação do Backend

Este ExecPlan é um documento vivo e deverá ser mantido de acordo com `.agent/PLANS.md`.

## Objetivo e resultado esperado

Criar a fundação técnica do Backend do Marketplace Vortex dentro de `backend/`.

Ao final deste plano deverá existir uma aplicação Spring Boot funcional que:

* utilize Java 21;
* utilize Spring Boot 4.1.x;
* utilize Maven Wrapper;
* possua estrutura inicial coerente com `docs/ARCHITECTURE.md`;
* consiga conectar-se a PostgreSQL;
* tenha PostgreSQL reproduzível localmente via Docker Compose;
* possua Flyway configurado;
* utilize profiles para diferentes ambientes;
* possua Actuator com health check;
* possua infraestrutura inicial de testes;
* consiga compilar;
* consiga executar os testes definidos neste plano;
* consiga iniciar localmente;
* consiga responder ao health check.

Nenhuma funcionalidade de negócio deverá ser implementada neste ExecPlan.

Ao final ainda NÃO existirão:

* cadastro;
* login;
* JWT;
* entidade `User`;
* entidade `Listing`;
* endpoints do marketplace.

O objetivo deste plano é construir somente uma base confiável sobre a qual os próximos módulos serão implementados.

---

## Contexto

O projeto é um monorepo:

```text
/
├── backend/
├── frontend/
├── docs/
├── .agent/
├── AGENTS.md
└── README.md
```

As especificações principais já estão definidas em:

```text
docs/REQUIREMENTS.md
docs/ARCHITECTURE.md
docs/API_CONTRACT.md
```

A arquitetura definiu para o Backend:

```text
Java 21
Spring Boot 4.1.x
Maven
PostgreSQL
Flyway
Spring Data JPA
Bean Validation
Actuator
Testcontainers
```

A aplicação utilizará arquitetura monolítica simples e organização por funcionalidade.

Package raiz previsto:

```text
com.vortex.marketplace
```

---

## Estado inicial

No início deste ExecPlan:

* o diretório `backend/` existe;
* nenhum projeto Spring Boot foi implementado;
* nenhum `pom.xml` existe no Backend;
* nenhuma entidade existe;
* nenhuma migration de domínio existe;
* PostgreSQL ainda não está configurado para esta aplicação;
* autenticação ainda não existe;
* endpoints de negócio ainda não existem.

Os documentos de especificação já devem existir e ser tratados como fonte de verdade.

---

## Escopo

Este plano inclui:

1. inicialização do projeto Spring Boot;
2. configuração do Maven;
3. Maven Wrapper;
4. estrutura inicial de packages;
5. configuração base da aplicação;
6. profiles;
7. PostgreSQL para desenvolvimento;
8. Docker Compose;
9. Flyway;
10. JPA;
11. Actuator;
12. configuração segura através de environment variables;
13. `.gitignore` adequado ao Backend;
14. `.env.example`, caso necessário;
15. infraestrutura básica de testes;
16. Testcontainers com PostgreSQL;
17. teste de inicialização do contexto;
18. validação do health check;
19. compilação e testes finais.

---

## Fora do escopo

Não implementar neste plano:

* `User`;
* `Listing`;
* `ListingCategory`;
* `ListingType`;
* repositories de domínio;
* services de domínio;
* controllers de domínio;
* endpoints `/api/v1`;
* cadastro;
* login;
* Spring Security;
* JWT;
* OAuth2 Resource Server;
* autorização;
* Swagger/OpenAPI;
* tratamento global de exceções;
* migrations `users`;
* migrations `listings`;
* filtros;
* paginação;
* CORS definitivo;
* Frontend;
* deploy.

Dependências relacionadas a essas funcionalidades deverão ser adicionadas somente quando chegarem aos respectivos ExecPlans, salvo dependências fundamentais explicitamente necessárias à fundação.

---

## Decisões técnicas deste plano

### Java

Utilizar:

```text
Java 21
```

Não alterar para Java 25 ou outra versão durante este plano.

---

### Spring Boot

Utilizar:

```text
Spring Boot 4.1.x
```

Versão inicial:

```text
4.1.0
```

Caso exista incompatibilidade real observada durante a implementação, ela deverá ser registrada e discutida antes de trocar a versão principal definida na arquitetura.

---

### Build

Utilizar Maven.

O Backend deverá possuir:

```text
backend/
├── mvnw
├── mvnw.cmd
├── .mvn/
└── pom.xml
```

Os comandos de desenvolvimento deverão preferencialmente utilizar Maven Wrapper.

No Windows:

```text
mvnw.cmd
```

Em sistemas Unix-like:

```text
./mvnw
```

---

### Coordenadas Maven

Configuração prevista:

```text
groupId:
com.vortex

artifactId:
marketplace

package:
com.vortex.marketplace
```

Nome descritivo da aplicação:

```text
Vortex Marketplace Backend
```

Packaging:

```text
jar
```

---

## Dependências desta fundação

Adicionar somente as dependências necessárias para esta etapa.

Previstas:

```text
Spring Web / Spring MVC
Spring Data JPA
Bean Validation
PostgreSQL Driver
Flyway
Spring Boot Actuator
Spring Boot Test
Testcontainers
Testcontainers PostgreSQL
Spring Boot Testcontainers
```

Não adicionar nesta etapa:

```text
Spring Security
OAuth2 Resource Server
springdoc-openapi
Lombok
MapStruct
```

Essas dependências serão adicionadas quando as funcionalidades correspondentes forem implementadas.

Não fixar manualmente versões de dependências gerenciadas pelo Spring Boot sem necessidade.

Antes de adicionar um artifact cujo nome ou compatibilidade dependa da versão atual do framework, verificar a documentação oficial ou metadata compatível com a versão utilizada.

---

## Estrutura inicial de packages

Criar somente a estrutura necessária neste momento.

Base:

```text
backend/src/main/java/com/vortex/marketplace/
```

Classe principal:

```text
MarketplaceApplication
```

Não criar antecipadamente controllers, services, repositories ou entidades vazias apenas para preencher diretórios.

Packages de domínio deverão surgir quando houver implementação real.

Configurações compartilhadas poderão ser adicionadas somente quando forem necessárias.

---

# Plano de implementação

## Milestone 1 — Inicializar o projeto Spring Boot

### Objetivo

Transformar `backend/` em um projeto Spring Boot Maven válido e compilável.

### Implementação

Criar o projeto utilizando as decisões deste documento.

Deverão existir:

```text
backend/pom.xml
backend/mvnw
backend/mvnw.cmd
backend/.mvn/
backend/src/main/java/
backend/src/main/resources/
backend/src/test/java/
```

Criar:

```text
com.vortex.marketplace.MarketplaceApplication
```

com a configuração padrão de inicialização Spring Boot.

Não adicionar código de domínio.

### Verificação

Executar:

Windows:

```text
cd backend
mvnw.cmd clean compile
```

Unix-like:

```text
cd backend
./mvnw clean compile
```

### Resultado esperado

* Maven executa corretamente;
* dependências são resolvidas;
* projeto compila;
* nenhum código de negócio foi criado.

Não considerar o milestone concluído caso a compilação falhe.

---

## Milestone 2 — Configurar PostgreSQL local

### Objetivo

Permitir que qualquer desenvolvedor inicialize o banco necessário ao Backend de forma simples.

### Implementação

Criar na raiz do monorepo:

```text
compose.yaml
```

O Compose deverá possuir apenas o serviço necessário nesta etapa:

```text
postgres
```

Configuração local prevista:

```text
database:
vortex_marketplace

user:
vortex

password:
vortex

port:
5432
```

Essas credenciais são exclusivamente locais e não representam secrets de produção.

Utilizar uma versão estável e adequada do PostgreSQL.

Persistir os dados localmente utilizando volume Docker nomeado.

Adicionar healthcheck do PostgreSQL quando adequado.

Não adicionar:

* Backend ao Compose;
* Frontend ao Compose;
* Redis;
* pgAdmin;
* outros serviços.

### Verificação

Executar:

```text
docker compose config
```

Depois:

```text
docker compose up -d
```

Verificar:

```text
docker compose ps
```

### Resultado esperado

PostgreSQL deve estar ativo e acessível localmente na porta configurada.

---

## Milestone 3 — Configuração da aplicação e profiles

### Objetivo

Separar configurações comuns e configurações dependentes do ambiente.

### Implementação

Criar:

```text
backend/src/main/resources/application.yml
backend/src/main/resources/application-dev.yml
backend/src/main/resources/application-test.yml
backend/src/main/resources/application-prod.yml
```

### application.yml

Deverá possuir somente configurações comuns aos ambientes.

Incluir quando adequado:

```text
spring.application.name
configuração JPA comum
configuração Flyway comum
configuração Actuator comum
```

Não colocar credenciais de produção.

---

### application-dev.yml

Deverá permitir executar a aplicação localmente contra PostgreSQL iniciado pelo `compose.yaml`.

Poderá utilizar valores padrão seguros exclusivamente para desenvolvimento local, compatíveis com o Compose.

Configurações deverão permitir override por environment variables.

Conceitualmente:

```text
DB_URL
DB_USERNAME
DB_PASSWORD
```

O profile de desenvolvimento deverá utilizar:

```text
ddl-auto=validate
```

O schema deverá ser controlado por Flyway.

---

### application-test.yml

Deverá conter apenas configurações realmente específicas dos testes.

Os testes de integração que utilizarem Testcontainers não deverão depender de um PostgreSQL previamente instalado ou iniciado manualmente.

---

### application-prod.yml

Não incluir credenciais.

Configurar datasource através de environment variables.

Nenhum secret de produção deverá possuir valor real versionado.

---

### Profile padrão

Evitar esconder diferenças de ambiente através de configurações implícitas.

Documentar posteriormente qual profile utilizar localmente.

Não definir `prod` como profile padrão.

---

### Verificação

Inspecionar as configurações e garantir que:

* nenhum secret real foi inserido;
* configurações de desenvolvimento funcionam com Compose;
* configuração de produção exige valores externos;
* Hibernate não utiliza `ddl-auto=update`.

---

## Milestone 4 — Configurar Flyway

### Objetivo

Garantir desde o início que evolução do banco seja controlada por migrations.

### Implementação

Configurar Flyway através das dependências e propriedades compatíveis com a versão utilizada.

Criar o diretório:

```text
backend/src/main/resources/db/migration/
```

Não criar migrations vazias apenas para ocupar numeração.

Não criar ainda:

```text
V1__create_users_table.sql
V2__create_listings_table.sql
```

Essas migrations pertencem aos ExecPlans que implementarem os respectivos domínios.

O objetivo desta etapa é somente garantir que Flyway esteja pronto para executar migrations reais quando elas forem criadas.

### Verificação

Com PostgreSQL ativo, iniciar a aplicação utilizando o profile `dev`.

Confirmar nos logs que:

* conexão com PostgreSQL foi estabelecida;
* Flyway inicializou corretamente;
* não houve tentativa do Hibernate de criar schema automaticamente;
* aplicação iniciou sem erros relacionados ao banco.

Não inventar mensagens de log esperadas literalmente; observar o comportamento real.

---

## Milestone 5 — Configurar Actuator

### Objetivo

Disponibilizar um health check simples para desenvolvimento e futuro deploy.

### Implementação

Configurar Actuator expondo somente o necessário nesta etapa.

Endpoint principal:

```text
GET /actuator/health
```

Não expor indiscriminadamente todos os endpoints do Actuator.

### Verificação

Com aplicação e PostgreSQL ativos:

```text
GET http://localhost:8080/actuator/health
```

Resultado esperado:

```text
HTTP 200
```

e status da aplicação saudável.

Não exigir que o JSON tenha campos além dos fornecidos naturalmente pela configuração definida.

---

## Milestone 6 — Configurar Testcontainers

### Objetivo

Permitir que testes de integração utilizem PostgreSQL real e descartável sem depender do banco de desenvolvimento.

### Implementação

Adicionar as dependências de teste necessárias para integração entre:

```text
Spring Boot
Testcontainers
PostgreSQL
```

Utilizar o mecanismo de integração recomendado e compatível com Spring Boot 4.1.x.

Preferir `@ServiceConnection` quando adequado.

Criar apenas a infraestrutura mínima necessária para compartilhar a configuração do container entre testes de integração.

Não criar abstrações complexas.

---

### Teste inicial

Criar um teste de integração capaz de validar que:

* contexto Spring inicia;
* PostgreSQL Testcontainer inicia;
* datasource é configurado corretamente;
* Flyway consegue inicializar;
* contexto encerra sem erro.

O teste não deverá depender do PostgreSQL do `compose.yaml`.

### Verificação

Com Docker disponível:

Windows:

```text
mvnw.cmd test
```

Unix-like:

```text
./mvnw test
```

Resultado esperado:

* exit code 0;
* nenhum teste falhando.

Caso Docker não esteja disponível, registrar claramente a limitação em vez de declarar o teste como aprovado.

---

## Milestone 7 — `.gitignore` e configuração segura

### Objetivo

Evitar versionamento de arquivos locais, secrets e artefatos de build.

### Implementação

Revisar o `.gitignore` da raiz e/ou Backend.

Garantir que arquivos como estes não sejam versionados quando aplicável:

```text
target/
.env
*.log
arquivos de IDE locais desnecessários
```

Não ignorar:

```text
.env.example
mvnw
mvnw.cmd
.mvn/
```

Se variáveis de ambiente forem necessárias para documentação local, criar:

```text
.env.example
```

na localização mais apropriada.

Esse arquivo deve possuir somente:

* nomes das variáveis;
* valores de exemplo não sensíveis;
* explicações curtas quando necessárias.

Nunca criar `.env` contendo secrets reais como parte do repositório.

---

## Milestone 8 — Verificação completa da fundação

### Objetivo

Demonstrar que a fundação funciona como uma unidade.

### Pré-condição

Docker disponível e funcionando.

### Passo 1 — Estado do banco

Executar:

```text
docker compose up -d
```

Verificar:

```text
docker compose ps
```

PostgreSQL deverá estar saudável ou operacional.

---

### Passo 2 — Testes automatizados

Dentro de `backend/`:

Windows:

```text
mvnw.cmd clean test
```

Unix-like:

```text
./mvnw clean test
```

Resultado esperado:

```text
BUILD SUCCESS
```

e nenhum teste falhando.

---

### Passo 3 — Build

Executar:

Windows:

```text
mvnw.cmd clean package
```

Unix-like:

```text
./mvnw clean package
```

Resultado esperado:

* exit code 0;
* JAR criado em `target/`.

---

### Passo 4 — Inicialização local

Iniciar a aplicação com o profile de desenvolvimento.

Exemplo conceitual:

```text
spring.profiles.active=dev
```

Utilizar a sintaxe adequada ao sistema operacional e ao comando escolhido.

Confirmar que:

* aplicação inicia;
* PostgreSQL conecta;
* Flyway inicializa;
* Hibernate valida o schema;
* servidor HTTP inicia.

---

### Passo 5 — Health check

Realizar:

```text
GET http://localhost:8080/actuator/health
```

Esperado:

```text
HTTP 200
```

---

### Passo 6 — Revisão do Git

Executar:

```text
git status
git diff
```

Verificar:

* somente arquivos relacionados à fundação foram alterados;
* nenhum secret foi versionado;
* nenhum arquivo temporário foi adicionado;
* nenhum código de domínio foi criado prematuramente.

---

# Validação final

Este ExecPlan somente poderá ser considerado tecnicamente concluído quando forem observados:

```text
[1] projeto Maven compila
[2] PostgreSQL inicia via Docker Compose
[3] Backend conecta ao PostgreSQL
[4] Flyway inicializa
[5] ddl-auto não está configurado como update/create
[6] Testcontainers consegue iniciar PostgreSQL nos testes
[7] testes passam
[8] package Maven passa
[9] aplicação inicia com profile dev
[10] /actuator/health retorna HTTP 200
[11] nenhum secret foi versionado
[12] git diff foi revisado
```

Se alguma verificação não puder ser executada, registrar explicitamente qual e por quê.

---

# Critérios de conclusão

O plano estará concluído quando existir uma fundação de Backend reproduzível e verificável, pronta para receber o domínio da aplicação.

O estado final esperado será conceitualmente:

```text
backend/
├── .mvn/
├── mvnw
├── mvnw.cmd
├── pom.xml
└── src/
    ├── main/
    │   ├── java/
    │   │   └── com/vortex/marketplace/
    │   │       └── MarketplaceApplication.java
    │   └── resources/
    │       ├── application.yml
    │       ├── application-dev.yml
    │       ├── application-test.yml
    │       ├── application-prod.yml
    │       └── db/
    │           └── migration/
    └── test/
        └── java/
            └── com/vortex/marketplace/
                └── infraestrutura mínima de teste
```

Na raiz deverá existir também:

```text
compose.yaml
```

Não deverá existir ainda implementação de usuário, autenticação ou anúncios.

---

## Progress

* [ ] Projeto Spring Boot inicializado.
* [ ] Maven Wrapper validado.
* [ ] PostgreSQL configurado no Docker Compose.
* [ ] Profiles configurados.
* [ ] Flyway configurado.
* [ ] Actuator configurado.
* [ ] Testcontainers configurado.
* [ ] Teste de integração da fundação aprovado.
* [ ] Build completo aprovado.
* [ ] Aplicação iniciada com profile dev.
* [ ] Health check validado.
* [ ] Diff final revisado.

---

## Surprises & Discoveries

Nenhuma descoberta relevante até o momento.

---

## Decision Log

### Decisão — Não implementar domínio nesta etapa

Motivo:

Separar problemas de infraestrutura dos problemas de negócio permite validar a fundação antes de adicionar autenticação, usuários e anúncios.

Consequência:

As migrations e entidades de domínio serão implementadas em ExecPlans posteriores.

### Decisão — Não adicionar Spring Security ainda

Motivo:

A segurança será implementada junto ao fluxo de autenticação, evitando ativar uma configuração padrão temporária que não corresponde ao contrato definitivo.

Consequência:

A dependência será adicionada no ExecPlan de autenticação.

### Decisão — Não adicionar OpenAPI ainda

Motivo:

Ainda não existem endpoints de domínio para documentar.

Consequência:

Springdoc será introduzido quando houver API real para expor e validar.

---

## Outcomes & Retrospective

Ainda não concluído.
