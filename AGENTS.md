# Diretrizes para Agentes de IA (AGENTS.md)

Este documento estabelece as regras de comportamento, arquitetura, qualidade e fluxos de trabalho para qualquer agente de IA que atue neste repositório.

---

## 1. Referências do Projeto
Qualquer agente deve ler e seguir os documentos de especificação do projeto localizados no diretório `docs/`:
- **PRD (Product Requirement Document):** [prd.md](file:///Users/vitor/facul/savour/docs/prd.md)
- **Especificação Técnica:** [spech_tech.md](file:///Users/vitor/facul/savour/docs/spech_tech.md)
- **Especificação de UI:** [spech_ui.md](file:///Users/vitor/facul/savour/docs/spech_ui.md)
- **Design System:** [design_system.md](file:///Users/vitor/facul/savour/docs/design_system.md) (Caso o arquivo seja criado no futuro. No momento, siga as especificações de estilização e componentes de UI descritas em [spech_ui.md](file:///Users/vitor/facul/savour/docs/spech_ui.md) e as boas práticas de CSS moderno / Tailwind).

---

## 2. Princípios de Comportamento (Karpathy CLAUDE.md)
Diretrizes comportamentais projetadas para mitigar erros comuns de agentes de IA durante a codificação:

### 2.1 Pense Antes de Codificar (Think Before Coding)
**Não presuma. Não esconda confusão. Torne os trade-offs explícitos.**
- Explicite as suas suposições antes de iniciar qualquer implementação. Se estiver incerto, pergunte ao usuário.
- Se houver múltiplas formas de resolver um problema ou interpretar um requisito, apresente os prós e contras de cada opção antes de tomar uma decisão.
- Se uma solução muito mais simples existir, sugira-a e evite complexidade desnecessária.
- Se as instruções forem ambíguas ou confusas, interrompa a execução imediatamente e peça esclarecimento ao usuário.

### 2.2 Simplicidade em Primeiro Lugar (Simplicity First)
**Escreva o menor código possível que resolva o problema. Nada especulativo.**
- Não implemente funcionalidades ou opções "para o futuro" que não foram solicitadas.
- Não introduza abstrações prematuras ou desnecessárias para trechos de código de uso único.
- Não adicione parâmetros extras ou configurações complexas que não constam na especificação.
- Evite tratamento de erros complexo para cenários teoricamente impossíveis.
- Se o código implementado ficou com 200 linhas mas poderia ser resolvido de forma limpa com 50 linhas, reescreva-o.

### 2.3 Alterações Cirúrgicas (Surgical Changes)
**Modifique apenas o estritamente necessário. Limpe apenas o que você tocou.**
- Não tente "melhorar", reformatar ou refatorar arquivos ou códigos adjacentes que não estejam diretamente relacionados à tarefa e que não estejam quebrados.
- Respeite e siga o estilo de código já estabelecido no projeto, mesmo que você prefira uma abordagem ou formatação diferente.
- Remova imports, variáveis, funções e arquivos que as suas alterações tornaram obsoletos. Não remova código morto pré-existente a menos que solicitado.
- **Critério de validação:** Cada linha modificada ou adicionada deve ter uma justificativa direta ligada à solicitação do usuário.

### 2.4 Execução Focada em Objetivos (Goal-Driven Execution)
- Defina critérios de aceitação e sucesso claros e mensuráveis antes de iniciar a tarefa.
- Ao corrigir um bug, escreva um caso de teste que reproduza a falha primeiro, e em seguida faça a alteração no código para que o teste passe.
- Em processos de refatoração, execute a suíte de testes antes e depois das alterações para certificar-se de que nenhuma regressão foi introduzida.

---

## 3. Busca de Documentação e APIs (Context7)
- **MANDATÓRIO:** Sempre utilize o MCP server do **Context7** (`context7`) quando realizar buscas por documentação atualizada de APIs, pacotes NPM, frameworks e padrões de desenvolvimento. Isso previne o uso de APIs obsoletas de frameworks da stack (como NestJS, React, Prisma, Zod, Tailwind) e acelera o alinhamento com as práticas de mercado mais recentes.

---

## 4. Harness de Qualidade (Linter) e Testes
Para garantir a integridade, segurança contra overbooking e consistência do Savour:

### 4.1 Linters e Análise Estática
- Antes de considerar qualquer tarefa finalizada, execute os comandos do linter do projeto (ex: `npm run lint` ou correspondente do NestJS/React) para corrigir pendências de estilo e erros estáticos.
- Garanta que todo código TypeScript compile sem erros (`tsc --noEmit`) e que não existam tipos declarados como `any` de forma injustificada.

### 4.2 Ciclo Contínuo de Testes
Toda modificação relevante ou nova funcionalidade deve estar acompanhada por testes automatizados em três níveis de granularidade:
- **Testes de Unidade:** Focados em lógica pura de domínio, regras de negócio (ex: validação de lotação de mesa, cálculo dinâmico de assentos livres e prevenção de double booking).
- **Testes de Integração:** Validação da camada de persistência (Prisma ORM integrado ao PostgreSQL local), controladores NestJS, injeção de dependências e eventos assíncronos (WebSockets/Redis).
- **Testes E2E (End-to-End) Críticos:** Simulação e validação da jornada completa:
  1. Cliente organizador buscando disponibilidade e confirmando reserva ou entrando na fila.
  2. Atualização em tempo real das notificações e do painel B2B da hostess.
  3. Ações de check-in, no-show e chamada de próximo da fila pela hostess.
- **Regra de Execução:** Sempre execute localmente a suíte de testes relevante antes de enviar as alterações para aprovação final.
