# Requisitos — Marketplace de Economia Circular Vortex

## 1. Objetivo do projeto

Desenvolver uma plataforma de economia circular voltada ao ambiente universitário, permitindo que estudantes anunciem itens para venda ou doação.

Exemplos de itens:

* livros;
* xerox e materiais acadêmicos;
* calculadoras científicas;
* componentes eletrônicos;
* jalecos;
* móveis;
* outros itens úteis à comunidade universitária.

A solução será uma aplicação web responsiva composta por:

* Backend com API REST;
* Frontend web;
* experiência mobile por meio de PWA.

---

# 2. Classificação dos requisitos

Neste documento:

* `OBR` = requisito obrigatório do desafio;
* `BONUS` = diferencial explicitamente citado no edital;
* `PLAN` = funcionalidade ou qualidade que decidimos implementar, embora não seja obrigatória no edital.

Requisitos `PLAN` podem ser reconsiderados caso comprometam a entrega dos requisitos obrigatórios.

---

# 3. Requisitos funcionais obrigatórios

## OBR-FR-001 — Visualizar anúncios

O sistema deve permitir a visualização pública dos anúncios cadastrados.

### Critérios de aceite

* Deve ser possível obter uma lista de anúncios.
* Os anúncios devem possuir informações suficientes para serem apresentados na interface.
* A listagem deve ser consumível pelo Frontend através da API REST.
* Os dados enviados pela API devem utilizar JSON.

---

## OBR-FR-002 — Criar anúncio

O sistema deve permitir o cadastro de um novo anúncio.

O anúncio deve possuir, no mínimo:

* título;
* descrição;
* categoria;
* preço ou indicação de que o item é uma doação;
* URL de uma imagem.

### Critérios de aceite

* O Frontend deve possuir um formulário para cadastrar o anúncio.
* O formulário deve enviar os dados ao Backend.
* O Backend deve persistir o anúncio.
* Depois de persistido, o anúncio deve poder aparecer nas listagens.

---

## OBR-FR-003 — Filtrar anúncios

O sistema deve permitir filtrar anúncios por categoria.

Categorias deverão representar grupos adequados aos itens do marketplace universitário.

### Critérios de aceite

* O usuário deve conseguir selecionar uma categoria.
* Somente anúncios compatíveis com o filtro devem ser retornados ou exibidos.
* O filtro deve ser suportado pela API.

---

## OBR-FR-004 — Excluir anúncio

O sistema deve permitir excluir anúncios cadastrados.

### Critérios de aceite

* Deve existir uma operação da API para exclusão.
* Depois da exclusão, o anúncio não deve mais aparecer nas listagens.

---

## OBR-FR-005 — Visualizar próprios anúncios

O usuário autenticado ou identificado deve conseguir visualizar os anúncios associados a ele.

### Critérios de aceite

* Deve existir uma forma de identificar o proprietário de um anúncio.
* O Frontend mobile deve possuir uma área onde o usuário visualize seus anúncios.

---

## OBR-FR-006 — Landing Page pública

A aplicação deve possuir uma Landing Page pública voltada principalmente à experiência Web/Desktop.

Ela deve:

* apresentar a proposta da plataforma;
* explicar a ideia de economia circular no campus;
* apresentar estatísticas simuladas do sistema;
* apresentar uma vitrine de anúncios recentes;
* possuir filtros básicos por categoria;
* possuir chamadas para ação para anunciar e buscar itens.

---

## OBR-FR-007 — Experiência mobile

A interface deve possuir uma experiência adequada para dispositivos móveis.

### Critérios de aceite

* O layout deve ser responsivo.
* As funcionalidades principais devem poder ser utilizadas no celular.
* A experiência mobile deve se aproximar da utilização de um aplicativo.

---

## OBR-FR-008 — PWA instalável

A aplicação deve funcionar como Progressive Web App.

### Critérios de aceite

* Deve existir um manifesto Web válido.
* Deve existir um Service Worker funcional.
* O navegador deve reconhecer a aplicação como instalável quando os critérios da plataforma forem atendidos.
* A aplicação instalada deve oferecer uma experiência adequada em dispositivos móveis.

---

# 4. Requisitos técnicos obrigatórios

## OBR-TR-001 — API REST

O Backend deve disponibilizar uma API REST estruturada para comunicação com o Frontend.

---

## OBR-TR-002 — JSON

A comunicação de dados da API deve utilizar JSON.

---

## OBR-TR-003 — Persistência

Os anúncios cadastrados devem possuir persistência funcional durante a utilização e os testes da aplicação.

---

## OBR-TR-004 — Frontend Web moderno

O Frontend deve ser desenvolvido utilizando tecnologias Web adequadas e possuir código estruturado.

---

## OBR-TR-005 — Responsividade

A aplicação deve funcionar adequadamente em diferentes tamanhos de tela, especialmente Desktop e Mobile.

---

# 5. Diferenciais do edital que serão implementados

O objetivo do projeto é implementar todos os diferenciais abaixo, desde que nenhum deles comprometa a conclusão e estabilidade dos requisitos obrigatórios.

## BONUS-001 — Autenticação de usuários

Implementar autenticação de usuários.

Planejamento atual:

* cadastro;
* login;
* autenticação baseada em JWT.

---

## BONUS-002 — Associação entre usuário e anúncio

Cada anúncio deverá possuir um usuário proprietário.

Isso permitirá identificar os anúncios pertencentes ao usuário atualmente autenticado.

---

## BONUS-003 — Validação de dados

O Backend deverá validar os dados recebidos.

Exemplos:

* campos obrigatórios;
* formatos inválidos;
* valores inválidos;
* dados inconsistentes.

As regras exatas serão definidas no documento de arquitetura e no contrato da API.

---

## BONUS-004 — Tratamento robusto de erros

A API deverá retornar erros de maneira previsível e estruturada.

O formato exato das respostas de erro será definido posteriormente no contrato da API.

---

## BONUS-005 — Banco de dados real

A aplicação utilizará um banco de dados real em vez de armazenamento exclusivamente em memória ou arquivo.

A tecnologia específica será registrada em `ARCHITECTURE.md`.

---

## BONUS-006 — TypeScript no Frontend

O Frontend será desenvolvido utilizando TypeScript.

---

## BONUS-007 — Interface polida

O Frontend deverá apresentar boa experiência de utilização.

Devem ser considerados, quando aplicáveis:

* feedback de carregamento;
* feedback de sucesso;
* feedback de erro;
* estados vazios;
* consistência visual;
* boa experiência mobile;
* transições adequadas.

---

## BONUS-008 — Cache e funcionamento offline da PWA

Será avaliada e, havendo tempo suficiente, implementada uma estratégia de cache que permita visualizar offline dados anteriormente carregados.

### Prioridade

Menor que:

* funcionamento correto da aplicação;
* autenticação;
* persistência;
* validações;
* responsividade;
* PWA instalável;
* deploy.

Este requisito poderá ser removido do escopo caso represente risco à entrega final.

---

## BONUS-009 — Deploy do Backend

A API deverá, preferencialmente, estar disponível publicamente em ambiente de produção.

---

## BONUS-010 — Deploy do Frontend

O Frontend deverá, preferencialmente, estar disponível publicamente em ambiente de produção e integrado à API publicada.

---

# 6. Requisitos internos de qualidade

Os requisitos desta seção não foram apresentados como funcionalidades obrigatórias do edital. São objetivos internos para aumentar a qualidade da entrega.

## PLAN-001 — Código compreensível

O código deve priorizar clareza e simplicidade.

Arquiteturas ou abstrações complexas não devem ser utilizadas sem necessidade concreta.

---

## PLAN-002 — Separação de responsabilidades

Backend e Frontend devem possuir responsabilidades claramente separadas.

---

## PLAN-003 — Testes automatizados

As funcionalidades críticas do Backend devem possuir testes automatizados.

Prioridade especial para:

* cadastro;
* autenticação;
* criação de anúncio;
* validações;
* filtros;
* exclusão;
* regras de autorização.

---

## PLAN-004 — Documentação da API

A API deverá possuir documentação que facilite sua compreensão e teste.

A tecnologia utilizada será definida posteriormente em `ARCHITECTURE.md`.

---

## PLAN-005 — Banco reproduzível

O ambiente de desenvolvimento deve permitir subir o banco de dados de maneira simples e reproduzível.

---

## PLAN-006 — Migrações de banco

Alterações estruturais no banco de dados deverão ser controladas por migrações.

---

## PLAN-007 — Verificação automática

O projeto deverá possuir uma forma confiável de executar:

* compilação;
* testes;
* verificações relevantes de qualidade.

Nenhuma verificação poderá ser declarada como bem-sucedida sem que tenha sido realmente executada.

---

# 7. Entregáveis obrigatórios

Além da aplicação, a entrega deverá possuir:

* repositório Git público;
* README principal;
* descrição do projeto;
* instruções de execução do Backend;
* instruções de execução do Frontend;
* lista das principais tecnologias;
* Diário de Bordo da utilização de IA;
* links de produção caso seja realizado deploy;
* vídeo de apresentação conforme formato solicitado no edital.

---

# 8. Diário de Bordo da IA

Durante todo o desenvolvimento deverá ser registrado o uso de ferramentas de Inteligência Artificial.

O registro deverá permitir posteriormente documentar:

* ferramentas utilizadas;
* prompts relevantes;
* problemas resolvidos com auxílio da IA;
* respostas incorretas ou incompletas produzidas pela IA;
* como esses problemas foram identificados;
* como foram corrigidos;
* decisões tomadas pelo desenvolvedor.

O arquivo utilizado será:

`docs/AI_LOG.md`

---

# 9. Fora do escopo

As seguintes funcionalidades não fazem parte dos requisitos atuais e não devem ser implementadas sem uma decisão explícita de alteração de escopo:

* pagamentos;
* integração com Pix;
* carrinho de compras;
* checkout;
* sistema de entregas;
* chat entre usuários;
* avaliações de vendedores;
* sistema complexo de notificações;
* microserviços;
* mensageria;
* arquitetura distribuída.

O Codex ou qualquer outra IA não deve adicionar funcionalidades fora do escopo por iniciativa própria.

---

# 10. Regra de prioridade

Em caso de conflito de tempo ou complexidade, utilizar a seguinte prioridade:

1. requisitos obrigatórios do edital;
2. estabilidade e ausência de bugs críticos;
3. deploy funcional;
4. diferenciais de alto valor e baixo/médio custo;
5. qualidade visual;
6. diferenciais opcionais de maior complexidade.

Uma funcionalidade bônus nunca deverá comprometer uma funcionalidade obrigatória.

---

# 11. Regra de alteração deste documento

Este documento representa os requisitos vigentes do projeto.

O Codex não deve:

* adicionar requisitos;
* remover requisitos;
* alterar requisitos;
* reinterpretar silenciosamente requisitos;
* transformar decisões de implementação em requisitos de produto.

Qualquer alteração relevante de escopo deverá ser feita explicitamente neste documento antes da implementação correspondente.

