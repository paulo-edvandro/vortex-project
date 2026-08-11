# PLANS.md — Planos de Execução do Vortex Marketplace

## 1. Objetivo

Este arquivo define como os planos de execução deste projeto devem ser escritos, mantidos e executados por agentes de IA.

Chamaremos esses documentos de:

`ExecPlan`

Um ExecPlan é utilizado para mudanças grandes, com múltiplas etapas ou que exijam decisões e verificações ao longo da implementação.

Exemplos:

* inicialização completa do Backend;
* implementação da autenticação;
* implementação do domínio de anúncios;
* preparação do deploy;
* construção do Frontend;
* implementação da PWA.

Um ExecPlan não substitui:

* `docs/REQUIREMENTS.md`;
* `docs/ARCHITECTURE.md`;
* `docs/API_CONTRACT.md`;
* `AGENTS.md`.

Ele transforma essas especificações em uma sequência concreta e verificável de implementação.

---

# 2. Localização

Os ExecPlans deste projeto devem ser armazenados em:

`.agent/execplans/`

Exemplo:

`.agent/execplans/01-backend-foundation.md`

---

# 3. Quando utilizar um ExecPlan

Criar um ExecPlan quando a tarefa:

* possuir múltiplas etapas dependentes;
* envolver vários arquivos ou componentes;
* exigir alterações arquiteturais relevantes;
* exigir banco de dados ou migrations;
* envolver autenticação ou segurança;
* envolver integração entre diferentes partes do sistema;
* exigir várias verificações para ser considerada concluída;
* for grande o suficiente para ser retomada posteriormente.

Não criar ExecPlan para alterações triviais.

Exemplos que normalmente NÃO precisam de ExecPlan:

* corrigir um typo;
* alterar uma mensagem;
* adicionar uma validação pequena isolada;
* corrigir um teste simples;
* ajustar uma configuração pequena.

---

# 4. Princípio fundamental

Todo ExecPlan deve permitir que um agente que não participou das conversas anteriores consiga compreender:

* qual é o objetivo;
* por que a mudança existe;
* qual é o estado inicial relevante;
* quais arquivos serão afetados;
* quais etapas executar;
* como verificar cada etapa;
* quando o trabalho poderá ser considerado concluído.

O ExecPlan deve ser suficientemente autossuficiente para permitir a continuidade do trabalho mesmo após perda do contexto da conversa anterior.

---

# 5. Relação com as fontes de verdade

Antes de escrever ou executar um ExecPlan, consultar os documentos relevantes:

1. `AGENTS.md`;
2. `docs/REQUIREMENTS.md`;
3. `docs/ARCHITECTURE.md`;
4. `docs/API_CONTRACT.md`;
5. `AGENTS.md` específico do diretório, quando existir;
6. código existente relacionado.

O ExecPlan não pode contradizer esses documentos.

Se houver contradição, ela deve ser apontada antes da implementação.

O ExecPlan não possui autoridade para alterar silenciosamente requisitos, arquitetura ou contrato da API.

---

# 6. Idioma

ExecPlans devem ser escritos preferencialmente em português.

Identificadores técnicos, nomes de classes, métodos, endpoints, comandos e arquivos deverão manter suas convenções naturais.

---

# 7. ExecPlan é um documento vivo

Um ExecPlan não deve permanecer congelado enquanto a implementação evolui.

Durante a execução, o agente deve atualizar o documento para registrar:

* progresso realizado;
* problemas encontrados;
* descobertas relevantes;
* decisões tomadas;
* verificações executadas;
* mudanças justificadas no plano.

O objetivo é permitir que outra execução consiga continuar o trabalho apenas examinando:

* o repositório atual;
* as especificações;
* o ExecPlan atualizado.

---

# 8. Seções obrigatórias

Todo ExecPlan deverá possuir as seguintes seções:

1. Objetivo e resultado esperado
2. Contexto
3. Estado inicial
4. Escopo
5. Fora do escopo
6. Arquivos e componentes envolvidos
7. Plano de implementação
8. Milestones
9. Validação
10. Critérios de conclusão
11. Progress
12. Surprises & Discoveries
13. Decision Log
14. Outcomes & Retrospective

Os nomes das quatro últimas seções deverão ser mantidos reconhecíveis mesmo que o restante do documento esteja em português.

---

# 9. Objetivo e resultado esperado

O início do ExecPlan deve explicar claramente:

* qual problema será resolvido;
* o que passará a existir depois da implementação;
* como um desenvolvedor poderá perceber que a mudança funciona.

Evitar objetivos abstratos.

Ruim:

`Implementar autenticação.`

Melhor:

`Permitir que um usuário se cadastre, faça login, receba um JWT e utilize esse token para acessar endpoints protegidos.`

---

# 10. Contexto

A seção de contexto deverá fornecer apenas as informações necessárias para compreender a tarefa.

Ela deverá citar caminhos concretos do repositório quando aplicável.

Exemplo:

`docs/API_CONTRACT.md` define que `POST /api/v1/auth/login` deve retornar um access token.

Não presumir que o agente lembra de conversas anteriores.

---

# 11. Estado inicial

Registrar o estado relevante antes da implementação.

Exemplo:

* projeto Spring Boot inicializado;
* PostgreSQL disponível através de Docker Compose;
* migrations de usuários já existentes;
* autenticação ainda não implementada.

Não precisa descrever todo o repositório, apenas aquilo que influencia a tarefa.

---

# 12. Escopo

Definir claramente o que será implementado.

Exemplo:

Para um ExecPlan de autenticação:

* cadastro;
* hash de senha;
* login;
* geração de JWT;
* validação de JWT;
* configuração do Spring Security;
* testes.

---

# 13. Fora do escopo

Definir explicitamente funcionalidades próximas que NÃO serão implementadas naquele plano.

Exemplo:

* refresh token;
* recuperação de senha;
* login social;
* verificação de email;
* autenticação multifator.

Isso evita expansão silenciosa da tarefa.

---

# 14. Arquivos e componentes envolvidos

Quando possível, indicar os componentes previstos.

Exemplo:

`backend/src/main/java/.../auth/`

`backend/src/main/java/.../security/`

`backend/src/test/java/.../auth/`

`backend/src/main/resources/db/migration/`

Não é obrigatório prever antecipadamente cada arquivo pequeno.

O objetivo é indicar onde a implementação deve ocorrer.

---

# 15. Plano de implementação

Descrever a sequência técnica em ordem lógica.

Cada etapa deve possuir:

* ação;
* finalidade;
* resultado esperado;
* forma de validação quando aplicável.

Evitar instruções vagas como:

`Configure a segurança corretamente.`

Preferir:

`Configure a SecurityFilterChain stateless permitindo acesso público às rotas definidas como públicas no API_CONTRACT.md e exigindo autenticação nas demais rotas protegidas.`

---

# 16. Milestones

Um ExecPlan grande deverá ser dividido em milestones.

Cada milestone deverá produzir um estado intermediário funcional ou verificável.

Exemplo:

`Milestone 1 — Persistência de usuários`

Ao final:

* tabela `users` criada por Flyway;
* entidade mapeada;
* repository funcional;
* migration validada em PostgreSQL.

Depois executar verificações correspondentes.

---

# 17. Milestones devem ser incrementais

Evitar:

`Milestone 1 — escrever todo o código`

`Milestone 2 — testar tudo`

Preferir:

`Milestone 1 — infraestrutura`

`Milestone 2 — domínio`

`Milestone 3 — endpoint`

`Milestone 4 — segurança`

com testes e verificações ao longo do processo.

Erros devem ser encontrados o mais cedo possível.

---

# 18. Não avançar ignorando falhas

Quando uma verificação importante de um milestone falhar:

1. investigar a causa;
2. corrigir o problema;
3. executar novamente a verificação;
4. somente então considerar o milestone concluído.

Não acumular falhas conhecidas para resolver somente no final.

Uma exceção pode existir se o próprio plano explicar que determinado comportamento só ficará funcional em milestone posterior.

Nesse caso, isso deve estar explicitamente documentado.

---

# 19. Validação

Todo ExecPlan deve definir como provar que o resultado funciona.

Apenas compilação normalmente não é suficiente.

Sempre que aplicável utilizar:

* compilação;
* testes unitários;
* testes de integração;
* execução da aplicação;
* requests HTTP;
* inspeção de banco;
* lint;
* type checking;
* build;
* testes manuais específicos.

A validação deverá utilizar comandos reais do projeto quando eles já forem conhecidos.

---

# 20. Evidência de funcionamento

Sempre que razoável, incluir uma pequena demonstração verificável do comportamento final.

Exemplo para uma API:

1. iniciar aplicação;
2. cadastrar usuário;
3. realizar login;
4. receber JWT;
5. chamar endpoint protegido;
6. observar resposta esperada.

O objetivo é provar comportamento, e não apenas existência de código.

---

# 21. Resultados esperados

Quando um comando possuir um resultado relevante para determinar sucesso, o ExecPlan deverá explicar o que observar.

Exemplo:

`./mvnw test`

Resultado esperado:

* exit code 0;
* nenhum teste falhando.

Não inventar a saída exata de comandos antes de executá-los.

---

# 22. Progress

Todo ExecPlan deverá possuir uma seção:

`## Progress`

Essa é a única seção na qual checklists são explicitamente recomendados.

Formato:

* [ ] etapa ainda não iniciada;
* [x] etapa concluída;
* [ ] etapa parcialmente concluída, acompanhada de observação textual.

Sempre que possível registrar data e horário ao atualizar progresso significativo.

Exemplo:

`- [x] 2026-08-10 — Migration inicial criada e validada.`

O progresso deve refletir o estado real do repositório.

---

# 23. Surprises & Discoveries

Todo ExecPlan deverá possuir:

`## Surprises & Discoveries`

Registrar fatos inesperados que tenham impacto real.

Exemplos:

* comportamento inesperado de uma biblioteca;
* incompatibilidade entre versões;
* migration que se comportou diferente do previsto;
* comportamento do PostgreSQL relevante para o projeto;
* requisito que revelou uma dificuldade não prevista.

Não preencher com observações triviais apenas para ocupar espaço.

Quando não houver descoberta:

`Nenhuma descoberta relevante até o momento.`

---

# 24. Decision Log

Todo ExecPlan deverá possuir:

`## Decision Log`

Registrar decisões tomadas durante a execução que não estavam totalmente determinadas antes.

Cada decisão relevante deverá registrar:

* decisão;
* motivo;
* consequência.

Exemplo:

`Decisão: utilizar PasswordEncoder baseado em BCrypt.`

`Motivo: é a estratégia definida em ARCHITECTURE.md e possui suporte direto pelo Spring Security.`

Não registrar novamente decisões óbvias já completamente definidas na arquitetura, salvo quando forem importantes para compreender uma mudança de direção.

---

# 25. Mudança de direção

Caso seja necessário desviar do plano original:

1. não esconder a mudança;
2. atualizar o ExecPlan;
3. registrar a razão em `Decision Log`;
4. atualizar os milestones afetados;
5. atualizar `Progress`.

Se a mudança contradizer requisitos, arquitetura ou API contract, parar a mudança estrutural e apontar o conflito ao desenvolvedor.

---

# 26. Outcomes & Retrospective

Todo ExecPlan deverá terminar ou manter:

`## Outcomes & Retrospective`

Durante a execução:

`Ainda não concluído.`

Quando concluído, registrar resumidamente:

* o que foi entregue;
* o que foi validado;
* o que ficou de fora;
* problemas encontrados;
* possíveis melhorias futuras.

Não adicionar funcionalidades futuras automaticamente.

---

# 27. Critérios de conclusão

Todo plano deverá declarar explicitamente quando a tarefa estará concluída.

Exemplo:

O plano de autenticação pode exigir:

* cadastro funcional;
* login funcional;
* JWT válido;
* endpoint protegido recusando usuário não autenticado;
* testes automatizados aprovados;
* build aprovado;
* diff revisado.

Uma tarefa não está concluída apenas porque os arquivos foram criados.

---

# 28. Git durante ExecPlans

ExecPlans NÃO autorizam o agente a fazer commits ou pushes automaticamente.

Durante a execução, o Codex poderá utilizar:

`git status`

`git diff`

`git log`

para inspeção.

O Codex NÃO deverá executar sem solicitação explícita:

`git commit`

`git push`

`git merge`

`git rebase`

`git reset --hard`

Ao alcançar um ponto que represente um bom checkpoint para commit, o agente poderá informar:

`Checkpoint adequado para commit.`

A decisão e execução do commit continuam pertencendo ao desenvolvedor.

---

# 29. Estado não limpo do Git

Se houver alterações anteriores no working tree:

* identificar quais arquivos já estavam modificados;
* preservar trabalho existente;
* não misturar alterações não relacionadas;
* não descartar mudanças do desenvolvedor.

O agente deve conseguir distinguir suas próprias alterações das alterações que já existiam.

---

# 30. Atualização do ExecPlan

O próprio arquivo de ExecPlan poderá ser atualizado pelo agente durante sua execução.

Essa atualização faz parte do trabalho.

Porém:

* não alterar `PLANS.md` automaticamente;
* não alterar requisitos ou arquitetura apenas para adaptar o plano à implementação;
* manter o ExecPlan coerente com o estado real.

---

# 31. Falhas de implementação

Se uma abordagem falhar, não apagar o histórico relevante como se nada tivesse ocorrido.

Quando a falha ensinar algo importante:

* registrar em `Surprises & Discoveries`;
* registrar eventual mudança em `Decision Log`;
* atualizar o plano.

Isso ajuda futuras execuções e também pode fornecer material relevante para o Diário de Bordo de IA.

---

# 32. Relação com AI_LOG

O ExecPlan não substitui:

`docs/AI_LOG.md`

O ExecPlan registra a evolução técnica da implementação.

O AI_LOG registra a utilização da Inteligência Artificial para fins do desafio técnico.

Quando ocorrer durante um ExecPlan:

* alucinação relevante da IA;
* solução incorreta sugerida pela IA;
* prompt especialmente importante;
* investigação técnica significativa;

o agente deverá sugerir ao desenvolvedor que considere registrar o episódio em `docs/AI_LOG.md`.

Não fazer o registro automaticamente sem solicitação.

---

# 33. Execução autônoma dentro do escopo

Uma vez aprovado um ExecPlan e solicitada sua execução, o Codex deverá seguir os milestones sem solicitar confirmação após cada pequena operação.

Ele pode:

* criar arquivos previstos;
* alterar código previsto;
* executar comandos de build;
* executar testes;
* corrigir falhas relacionadas;
* atualizar o próprio ExecPlan.

Ele não precisa perguntar:

`Posso criar a próxima classe?`

`Posso executar os testes?`

`Posso corrigir este erro de compilação?`

Essas operações já fazem parte da execução aprovada.

---

# 34. Limites da autonomia

A autonomia termina quando surgir necessidade de:

* alterar requisito;
* alterar arquitetura relevante;
* quebrar contrato da API;
* adicionar dependência estrutural não prevista;
* remover funcionalidade planejada;
* introduzir risco significativo ao projeto;
* realizar operação destrutiva;
* fazer commit ou push sem autorização.

Nessas situações, o agente deverá explicar o problema antes da mudança correspondente.

---

# 35. Não solicitar próximos passos desnecessariamente

Enquanto ainda houver milestones claramente definidos e executáveis no ExecPlan, o agente deve continuar trabalhando dentro do escopo solicitado.

Não encerrar prematuramente com:

`Qual é o próximo passo?`

se o próprio ExecPlan já define a próxima etapa.

---

# 36. Tamanho dos planos

ExecPlans devem ser detalhados o suficiente para evitar ambiguidades, mas não devem virar documentação redundante de todo o projeto.

Incluir o contexto necessário para aquela mudança.

Não copiar integralmente:

* REQUIREMENTS.md;
* ARCHITECTURE.md;
* API_CONTRACT.md.

Resumir no plano as informações relevantes e apontar os arquivos de origem.

---

# 37. Código dentro do plano

Não escrever antecipadamente grandes implementações completas dentro do ExecPlan.

O plano deve especificar:

* comportamento;
* estrutura;
* decisões;
* etapas;
* validações.

Pequenos exemplos podem ser utilizados quando eliminarem ambiguidade.

O código real pertence à implementação.

---

# 38. Template padrão

Um novo ExecPlan deve seguir aproximadamente esta estrutura:

# <Nome objetivo do plano>

Este ExecPlan é um documento vivo e deverá ser mantido de acordo com `.agent/PLANS.md`.

## Objetivo e resultado esperado

Explique o comportamento que existirá ao final e como observá-lo.

## Contexto

Explique o contexto necessário para executar esta tarefa sem depender de conversas anteriores.

## Estado inicial

Descreva o estado relevante atual do projeto.

## Escopo

Descreva o que será implementado.

## Fora do escopo

Descreva explicitamente o que não será implementado.

## Arquivos e componentes envolvidos

Liste ou descreva os principais locais afetados.

## Plano de implementação

Explique tecnicamente a sequência de trabalho.

## Milestones

### Milestone 1 — <nome>

Explique:

* objetivo;
* implementação;
* verificação;
* resultado esperado.

### Milestone 2 — <nome>

Repita conforme necessário.

## Validação

Descreva a validação final e os comandos necessários.

## Critérios de conclusão

Descreva claramente quando o plano poderá ser considerado concluído.

## Progress

* [ ] Etapa inicial.
* [ ] Próxima etapa.
* [ ] Validação final.

## Surprises & Discoveries

Nenhuma descoberta relevante até o momento.

## Decision Log

Nenhuma decisão adicional até o momento.

## Outcomes & Retrospective

Ainda não concluído.

---

# 39. Regra final

Um bom ExecPlan deve responder claramente:

`O que estamos construindo?`

`Por que estamos construindo?`

`Como será construído?`

`Em qual ordem?`

`Como sabemos que cada etapa funcionou?`

`Como sabemos que o trabalho terminou?`

O objetivo não é produzir documentação por burocracia.

O objetivo é permitir que o Codex execute trabalhos complexos com menos ambiguidades, menos regressões e maior capacidade de recuperação caso o contexto seja perdido.

