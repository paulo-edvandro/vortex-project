# AGENTS.md — Vortex Marketplace

## 1. Objetivo

Este arquivo contém as instruções permanentes para agentes de IA que trabalham neste repositório.

O projeto faz parte de um desafio técnico para processo seletivo e deve ser desenvolvido com prioridade em:

1. funcionamento correto;
2. cumprimento dos requisitos;
3. simplicidade;
4. qualidade;
5. testes;
6. segurança;
7. facilidade de manutenção;
8. facilidade de explicação pelo candidato durante a avaliação.

A IA deve atuar como uma ferramenta de desenvolvimento guiado.

Ela não deve assumir o papel de proprietária das decisões de produto ou arquitetura.

---

# 2. Idioma

Documentação, explicações e comunicação com o desenvolvedor devem ser preferencialmente em português.

Código, nomes de classes, métodos, variáveis, arquivos técnicos e outros identificadores devem seguir as convenções definidas para cada tecnologia e normalmente utilizar inglês.

Exemplo:

```text
Documentação → português

Código:
ListingService
createListing()
ownerId
createdAt
```

Não traduzir nomes técnicos quando isso prejudicar convenções comuns da tecnologia utilizada.

---

# 3. Estrutura principal do repositório

Este projeto utiliza monorepo.

Estrutura principal:

```text
/
├── backend/
├── frontend/
├── docs/
├── .agent/
├── AGENTS.md
└── README.md
```

Backend e Frontend são aplicações independentes dentro do mesmo repositório.

Não misturar responsabilidades entre elas.

---

# 4. Fontes de verdade

Antes de implementar qualquer funcionalidade relevante, identificar e consultar os documentos aplicáveis.

A ordem de autoridade é:

```text
1. Edital original do desafio
2. docs/REQUIREMENTS.md
3. docs/ARCHITECTURE.md
4. docs/API_CONTRACT.md
5. AGENTS.md aplicável ao diretório
6. ExecPlan da tarefa atual, quando existir
7. implementação existente
```

Os documentos possuem responsabilidades diferentes:

## REQUIREMENTS.md

Define:

> O que o sistema deve fazer.

## ARCHITECTURE.md

Define:

> Como o sistema será estruturado tecnicamente.

## API_CONTRACT.md

Define:

> Como Backend e Frontend se comunicam por HTTP.

## AGENTS.md

Define:

> Como o agente deve trabalhar.

## ExecPlan

Define:

> Como uma tarefa ou etapa específica será executada.

Não duplicar desnecessariamente informações entre esses arquivos.

---

# 5. Leitura obrigatória antes de implementar

Antes de iniciar uma implementação relevante:

1. leia as instruções `AGENTS.md` aplicáveis;
2. leia `docs/REQUIREMENTS.md`;
3. leia as partes relevantes de `docs/ARCHITECTURE.md`;
4. leia as partes relevantes de `docs/API_CONTRACT.md`;
5. leia o ExecPlan atual, caso a tarefa utilize um;
6. examine o código existente relacionado à alteração.

Não começar alterando arquivos antes de compreender o contexto necessário.

---

# 6. Não inventar requisitos

Nunca adicionar funcionalidades apenas porque parecem úteis.

Não criar por iniciativa própria:

* novos endpoints;
* novos campos;
* novas entidades;
* novas dependências;
* novas integrações;
* novas regras de negócio;
* novas funcionalidades de produto.

Se alguma funcionalidade não estiver especificada, não presumir que ela deve existir.

Exemplo:

se o projeto não possui favoritos especificados, não criar:

```text
Favorite
POST /favorites
GET /favorites
```

apenas porque marketplaces normalmente possuem favoritos.

---

# 7. Não alterar especificações silenciosamente

O agente não pode alterar unilateralmente:

* REQUIREMENTS.md;
* ARCHITECTURE.md;
* API_CONTRACT.md;
* decisões estruturais relevantes.

Se encontrar uma inconsistência:

1. identifique-a;
2. explique objetivamente o problema;
3. indique os arquivos envolvidos;
4. proponha uma solução;
5. não faça uma mudança arquitetural ou de contrato silenciosamente.

Pequenos detalhes de implementação que não contradigam as especificações podem ser decididos normalmente.

---

# 8. Escopo das tarefas

Implemente somente o escopo solicitado.

Não utilizar uma tarefa pequena como oportunidade para realizar grandes refatorações não solicitadas.

Exemplo:

se a tarefa é:

```text
Adicionar validação do cadastro
```

não aproveitar para:

```text
renomear todos os packages
trocar biblioteca
reescrever autenticação
reformatar todo o projeto
```

Alterações auxiliares pequenas e necessárias são permitidas.

---

# 9. Inspecionar antes de modificar

Antes de alterar código existente:

1. localize a implementação relacionada;
2. leia os arquivos relevantes;
3. entenda as dependências da alteração;
4. verifique testes existentes;
5. somente então faça a modificação.

Não substituir implementações inteiras sem primeiro entender o código presente.

---

# 10. Simplicidade

Entre duas soluções tecnicamente adequadas, prefira aquela que seja:

* mais simples;
* mais legível;
* mais fácil de testar;
* mais fácil de manter;
* mais fácil de explicar.

Não utilizar complexidade apenas para demonstrar conhecimento técnico.

Evitar overengineering.

---

# 11. Dependências

Antes de adicionar uma nova dependência:

1. verifique se a funcionalidade já pode ser atendida pela stack existente;
2. verifique se a dependência está prevista em `ARCHITECTURE.md`;
3. avalie se ela realmente reduz complexidade;
4. evite bibliotecas redundantes.

Dependências não previstas que alterem significativamente a arquitetura não devem ser adicionadas automaticamente.

---

# 12. Compatibilidade com versões

Nunca assumir APIs de bibliotecas apenas pela memória.

Antes de utilizar comportamento específico dependente de versão:

1. verifique a versão utilizada pelo projeto;
2. consulte documentação disponível quando necessário;
3. implemente utilizando APIs compatíveis com a versão real.

Não misturar exemplos de versões antigas e novas de frameworks.

---

# 13. Implementação incremental

Mudanças relevantes devem ser desenvolvidas em etapas pequenas e verificáveis.

Preferir:

```text
implementar
↓
compilar
↓
testar
↓
corrigir
↓
revisar
```

em vez de implementar várias funcionalidades grandes antes da primeira verificação.

---

# 14. Compilação

Depois de alterações de código, execute a compilação ou equivalente adequado ao projeto sempre que possível.

Não declarar que o projeto compila sem realmente executar a verificação.

Caso a compilação não possa ser executada, informar isso explicitamente.

---

# 15. Testes

Testes fazem parte da implementação.

Ao alterar comportamento:

1. identifique testes existentes relacionados;
2. atualize ou adicione testes quando necessário;
3. execute os testes relevantes;
4. execute uma suíte mais ampla quando a alteração justificar.

Nunca:

* apagar um teste apenas porque falhou;
* desabilitar um teste para obter build verde;
* reduzir uma assertion apenas para fazer o teste passar;
* modificar uma regra correta para satisfazer um teste incorreto.

Quando houver falha, investigar a causa.

---

# 16. Verificação obrigatória

Nunca afirmar:

```text
"todos os testes passaram"
"build concluído"
"funciona corretamente"
"migration executada com sucesso"
```

sem ter executado e observado a verificação correspondente.

Diferenciar claramente:

```text
implementado
```

de:

```text
verificado
```

---

# 17. Tratamento de erros durante o desenvolvimento

Quando um comando falhar:

1. leia o erro;
2. identifique a causa provável;
3. investigue o código ou configuração relacionada;
4. corrija a causa;
5. execute novamente a verificação.

Não aplicar alterações aleatórias sucessivamente apenas para tentar obter sucesso.

---

# 18. Segurança

Nunca inserir no código ou versionar:

* passwords reais;
* tokens;
* JWT secrets;
* chaves privadas;
* credenciais;
* connection strings de produção contendo segredos.

Utilizar variáveis de ambiente quando aplicável.

Nunca imprimir secrets completos em logs ou respostas.

---

# 19. Dados sensíveis

Não registrar em logs:

```text
password
passwordHash
JWT completo
JWT secret
credenciais
```

Não expor dados que não fazem parte explicitamente do contrato público da API.

---

# 20. Banco de dados

Não modificar migrations já aplicadas apenas para ajustar uma mudança posterior.

Quando o schema precisar evoluir, criar uma nova migration.

Não substituir migrations por mecanismos automáticos de alteração de schema quando a arquitetura definir Flyway como fonte de controle.

---

# 21. API

O arquivo:

```text
docs/API_CONTRACT.md
```

é a referência para o contrato HTTP.

Não alterar silenciosamente:

* URL;
* método HTTP;
* status code;
* request;
* response;
* nomes de propriedades;
* regras de autenticação;
* paginação;
* formato de erros.

Backend e Frontend devem implementar o mesmo contrato.

---

# 22. Qualidade de código

Evitar:

* métodos excessivamente grandes;
* duplicação evidente;
* nomes genéricos sem significado;
* abstrações desnecessárias;
* comentários explicando código óbvio;
* classes que acumulam responsabilidades não relacionadas.

Preferir nomes que expressem intenção.

---

# 23. Comentários

Comentários devem explicar principalmente:

```text
por que
```

e não simplesmente:

```text
o que
```

Evitar:

```java
// busca usuário
User user = repository.findById(id);
```

Comentários são apropriados quando explicam decisões não evidentes ou regras importantes.

---

# 24. Formatação

Respeitar o estilo existente do código.

Não reformatar arquivos não relacionados à tarefa.

Não gerar grandes diffs apenas por mudanças de whitespace ou formatação.

---

# 25. Git

O agente NÃO deve executar automaticamente:

```text
git commit
git push
git reset --hard
git rebase
git merge
```

a menos que o desenvolvedor solicite explicitamente.

É permitido utilizar comandos somente de inspeção, como:

```text
git status
git diff
git log
```

quando forem úteis para verificar o trabalho.

Nunca descartar alterações existentes do desenvolvedor.

Nunca sobrescrever trabalho não relacionado à tarefa.

---

# 26. Estado inicial antes de uma alteração

Antes de uma tarefa significativa, quando apropriado:

1. execute `git status`;
2. identifique alterações já existentes;
3. preserve alterações que não pertencem à tarefa atual.

Não assumir que toda modificação presente no working tree foi criada pelo agente.

---

# 27. Revisão do diff

Depois de uma implementação relevante:

1. execute `git status`;
2. execute `git diff` apropriado;
3. verifique se somente arquivos necessários foram modificados;
4. procure alterações acidentais;
5. corrija problemas encontrados antes de concluir.

---

# 28. Resumo ao concluir uma tarefa

Ao terminar uma implementação, forneça um resumo curto contendo:

## Alterações

O que foi implementado.

## Arquivos

Principais arquivos criados ou alterados.

## Verificações

Comandos realmente executados e seus resultados.

## Pendências

Qualquer problema, limitação ou verificação que não tenha sido possível realizar.

Não esconder falhas.

---

# 29. Diário de IA

O projeto possui:

```text
docs/AI_LOG.md
```

para registrar uso relevante de Inteligência Artificial exigido pelo processo seletivo.

Não modificar esse arquivo automaticamente em toda tarefa, pois o registro deve possuir contexto útil e ser revisado pelo desenvolvedor.

Quando uma tarefa produzir algo relevante para o Diário de IA, como:

* decisão arquitetural importante;
* prompt complexo;
* bug difícil;
* resposta incorreta da IA;
* correção de uma alucinação;
* investigação técnica relevante;

mencionar ao final:

```text
Sugestão: registrar esta interação no AI_LOG.
```

O desenvolvedor decidirá quando realizar o registro.

---

# 30. Alucinações e incerteza

Quando não souber algo necessário para a implementação:

* não inventar;
* não fingir certeza;
* investigar primeiro;
* consultar documentação quando apropriado;
* apontar a incerteza caso ela permaneça.

Uma resposta explícita de incerteza é preferível a uma implementação baseada em uma suposição oculta.

---

# 31. Documentação externa

Quando uma implementação depender de detalhes específicos de:

* Spring;
* Java;
* PostgreSQL;
* React;
* Vite;
* bibliotecas;
* ferramentas de deploy;

preferir documentação oficial e compatível com a versão utilizada.

Evitar basear decisões importantes em snippets aleatórios sem verificar compatibilidade.

---

# 32. ExecPlans

Mudanças complexas poderão possuir um ExecPlan específico em:

```text
.agent/execplans/
```

Quando a tarefa indicar um ExecPlan:

1. leia `.agent/PLANS.md`;
2. leia integralmente o ExecPlan correspondente;
3. siga suas etapas;
4. atualize seu progresso conforme as regras definidas em `PLANS.md`.

Não criar ExecPlans para alterações triviais.

---

# 33. AGENTS.md específicos

Subdiretórios poderão possuir seus próprios arquivos `AGENTS.md`.

Exemplo:

```text
backend/AGENTS.md
frontend/AGENTS.md
```

Esses arquivos devem conter regras específicas daquela aplicação.

As instruções mais específicas complementam as instruções globais deste documento.

Não repetir desnecessariamente toda a documentação global nesses arquivos.

---

# 34. Prioridade de entrega

Quando houver pressão de tempo, priorizar:

```text
1. requisito obrigatório funcionando
2. estabilidade
3. testes críticos
4. deploy
5. bônus de alto valor
6. refinamentos
```

Não sacrificar requisitos obrigatórios para implementar funcionalidades opcionais.

---

# 35. Critério de conclusão

Uma tarefa não está concluída apenas porque o código foi escrito.

Sempre que aplicável, concluir significa:

```text
implementação
+
compilação
+
testes
+
revisão do diff
```

Se alguma dessas etapas não puder ser realizada, informar explicitamente.

---

# 36. Regra principal

Não tente impressionar com complexidade.

Tente entregar software:

```text
correto
simples
testado
seguro
consistente
explicável
```

Esse é o padrão esperado durante todo o desenvolvimento.
