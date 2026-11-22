# Iniciando projeto

Guia para colocar o time de agentes da Dunbreeze para trabalhar em qualquer repositório. Primeiro a estrutura que o projeto precisa, depois cada agente, como usar e o que ele espera encontrar.

## Checklist de um projeto novo

- [ ] Agentes instalados em `~/.claude/agents/` (uma vez por máquina, veja a seção Instalação)
- [ ] MCP do Linear conectado (para `scrum-master` e `qa`)
- [ ] Node.js com Playwright (para o `designer` exportar telas)
- [ ] `CLAUDE.md` na raiz do repositório
- [ ] `.claude/stack.md` gerado pelo tech lead na primeira sessão
- [ ] `design/telas.md` criado (se o projeto tem interface)
- [ ] Time e status do Linear anotados no `CLAUDE.md`

## Estrutura esperada no repositório

```
meu-projeto/
├── CLAUDE.md                 # regras do projeto: você escreve (modelo abaixo)
├── .claude/
│   ├── stack.md              # perfil técnico: o tl-mapeador gera
│   └── agents/               # opcional: versões dos agentes só deste projeto
├── design/
│   ├── telas.md              # registro de telas: o designer mantém
│   ├── tokens.css            # tokens visuais
│   ├── prototipos/           # telas em HTML
│   └── export/               # lotes para subir no Figma
└── (código, testes, CI...)
```

| Arquivo | Quem cria | Quem usa | Obrigatório? |
|---|---|---|---|
| `CLAUDE.md` | Você, uma vez por projeto | Todos os agentes | Sim |
| `.claude/stack.md` | `tl-mapeador` (automático) | tech-lead, tl-*, qa, scrum-master | Sim, mas é gerado sozinho |
| `design/telas.md` | `designer` | designer, tech-lead, qa | Só com interface |
| Testes e comando de teste funcionando | Projeto | tl-testes, qa | Muito recomendado |
| Cards no Linear com CA, RN, RF e RNF | `scrum-master` | tech-lead, qa | Para o fluxo com Linear |

Faça commit do `CLAUDE.md`, do `stack.md` e do `telas.md`. Assim qualquer sessão (e qualquer dev) parte do mesmo contexto.

## Modelo de CLAUDE.md

Copie para a raiz e preencha. Seja curto: o que não está aqui o agente descobre no código.

```markdown
# <Nome do projeto>

## Cliente e objetivo
<Empresa cliente, setor, o que o sistema resolve, em 2 ou 3 frases.>

## Regras que os agentes devem seguir
- Idioma do código: <inglês>; idioma de textos para o usuário: <português>
- Nunca commitar sem eu pedir
- Branch: <feature/DUN-123-descricao>
- Commits: <Conventional Commits, ex.: feat(pedidos): ...>
- Dados sensíveis: <ex.: CPF, saldo e patrimônio nunca em log>

## Linear
- Time: <Dunbreeze>
- Prefixo dos cards: <DUN>
- Status de QA aprovado: <Pronto>
- Status de QA reprovado: <Em desenvolvimento>

## Figma
- Arquivo do design system: <link>
- Arquivo de telas deste projeto: <link>

## Ambientes
- Local: <como subir, ex.: docker compose up>
- Homologação: <URL> (só leitura para agentes)
- Produção: agentes nunca acessam

## Decisões de arquitetura
- <ex.: regras de negócio só na camada de serviço, nunca no controller>
```

## Modelo de design/telas.md

```markdown
# Registro de telas

| Tela | Rota | Status | Protótipo | Lote Figma | Componente no código | Última revisão |
|---|---|---|---|---|---|---|
| Login | /login | implementada | design/prototipos/login.html | export/2026-11-22 | src/pages/Login.tsx | 2026-11-22 |
```

Status possíveis: protótipo, aprovada, exportada, implementada, divergente.

## Os agentes

### tech-lead (orquestrador)

**O que faz:** entende o projeto, planeja, quebra o trabalho e delega para os subagentes. Só dá como pronto depois do revisor e do QA.

**Precisa no projeto:** `CLAUDE.md`, `.claude/stack.md` (ele gera se não existir), git, e comandos de teste e lint funcionando.

**Como usar:** abra como sessão principal.

```bash
cd meu-projeto
claude --agent tech-lead
```

Exemplos de prompt:
- "Primeira vez neste projeto: mapeie a stack e crie o stack.md."
- "Implemente o DUN-42. Leia o card, planeje, delegue e só me chame quando o QA aprovar."
- "O cliente quer exportar o relatório de margem em PDF com a marca dele. Planeje antes de codar e me mostre os critérios de aceite."

### Subagentes do tech lead

Você normalmente não chama esses agentes: o tech lead chama. Mas pode chamar pelo nome quando quiser só uma parte.

| Subagente | Faz | Precisa | Exemplo direto |
|---|---|---|---|
| `tl-mapeador` | Lê o repositório e escreve o `stack.md`; responde "onde fica X" | Só o repositório | "Use o tl-mapeador para descobrir onde fica o cálculo de comissão." |
| `tl-implementador` | Implementa uma tarefa bem definida | `stack.md`, critérios de aceite | "Use o tl-implementador para adicionar paginação em /pedidos, 50 por página." |
| `tl-testes` | Escreve e roda testes para os critérios | Framework de testes configurado | "Use o tl-testes para cobrir a regra de desconto máximo de 15%." |
| `tl-revisor` | Revisa o diff: bugs, segurança, LGPD, performance | git com mudanças | "Use o tl-revisor no diff atual antes de eu abrir o PR." |

### scrum-master

**O que faz:** transforma ideia, transcrição ou e-mail de cliente em épicos e histórias no Linear, com critérios de aceite (CA), regras de negócio (RN), requisitos funcionais (RF) e não funcionais (RNF) numerados. Mostra a tabela antes de criar.

**Precisa:** MCP do Linear conectado; seção Linear do `CLAUDE.md` (time e prefixo). O `stack.md` é opcional, mas deixa as notas técnicas melhores.

**Exemplos:**
- "Use o scrum-master para transformar esta transcrição da reunião com o cliente em cards: [cole a transcrição]."
- "Use o scrum-master para quebrar o épico 'Portal do investidor' em histórias de até 3 dias."

### qa

**O que faz:** valida cada card do Linear item por item (CA, RN, RF, RNF) com evidência: teste existente, teste novo, execução real ou leitura de código. Comenta o parecer no card: APROVADO, REPROVADO ou BLOQUEADO.

**Precisa:** MCP do Linear; cards no formato do scrum-master; `stack.md` (para saber rodar e testar); o projeto subindo localmente; Playwright se for testar telas. Status de aprovado e reprovado no `CLAUDE.md` evitam a pergunta no início.

**Skills:** liste no campo `skills:` do arquivo `qa.md` (já vem com `break-ui`).

**Exemplos:**
- "Use o qa para validar o DUN-42 contra a branch atual."
- Varredura: `claude --agent qa` e depois "Valide todos os cards em Em revisão do ciclo atual e me dê o resumo."

### designer (rascunho)

**O que faz:** prototipa cada tela como HTML dentro do projeto (`design/prototipos/`), você revisa no preview/canvas do Claude ou no navegador, e quando aprovar ele exporta um lote de SVG e PNG em `design/export/<data>/` para você arrastar para o Figma de uma vez. Não gasta créditos do MCP do Figma. O código continua sendo a verdade final.

**Precisa:** `design/telas.md` e `design/tokens.css` (ele cria se faltar), Node.js com Playwright para exportar, e os componentes base no código. Falta definir a lista final de skills.

**Agente independente:** nenhum outro agente chama o designer. O melhor jeito é abrir uma sessão só dele, porque ele conversa com você até a aprovação: `claude --agent designer`.

**Exemplos:**
- "Use o designer para prototipar o dashboard financeiro do diretor, com KPIs de receita, margem e EBITDA."
- "Use o designer para exportar para o Figma as telas aprovadas desta semana."

## Fluxo completo de uma feature

1. **scrum-master:** transcrição da reunião vira os cards DUN-50 a DUN-54.
2. **designer:** prototipa a tela do DUN-51 no projeto, você aprova e sobe o lote no Figma.
3. **tech-lead:** "Implemente DUN-50 a DUN-54 na ordem de dependência."
4. Dentro dele: implementador, testes, revisor e, por fim, o **qa** comenta o parecer em cada card.

## Instalação

### Pré-requisitos
- Claude Code instalado (`claude --version` responde).
- Git e Node.js (o Node é necessário para o Playwright, se for testar telas).

### 1. Copie os agentes

Clone este repositório e ligue a pasta `agents/` à pasta de agentes do seu usuário (veja o README). Agentes ali valem para todos os projetos.

macOS e Linux:
```bash
mkdir -p ~/.claude/agents
cp ~/Downloads/agents/*.md ~/.claude/agents/
```

Windows (PowerShell):
```powershell
New-Item -ItemType Directory -Force "$HOME\.claude\agents"
Copy-Item "$HOME\Downloads\agents\*.md" "$HOME\.claude\agents\"
```

### 2. Conecte o Linear

```bash
claude mcp add --transport http linear https://mcp.linear.app/mcp --scope user
```

Depois abra `claude`, digite `/mcp` e faça login. O `--scope user` deixa a conexão valendo em todos os projetos.

### 3. Confira

Dentro do Claude Code, digite `/agents`. Os 8 agentes devem aparecer: tech-lead, tl-mapeador, tl-implementador, tl-testes, tl-revisor, scrum-master, qa e designer (e os que você criar depois).

### 4. Primeiro uso num projeto

```bash
cd meu-projeto
# crie o CLAUDE.md a partir do modelo acima
claude --agent tech-lead
```

E peça: "Primeira vez neste projeto: mapeie a stack e crie o stack.md."

### Se algo não funcionar
- **`--agent` não existe na sua versão:** atualize o Claude Code. Se ainda assim faltar, abra `claude` e diga "Atue como o tech-lead definido em ~/.claude/agents/tech-lead.md".
- **Agente não aparece em `/agents`:** confira se o arquivo começa com o bloco `---` e tem `name` e `description`.
- **Linear sem resposta:** rode `/mcp` e faça login de novo.
- **Agente diferente só num projeto:** copie o arquivo para `<projeto>/.claude/agents/` e edite lá. A versão do projeto tem prioridade.
