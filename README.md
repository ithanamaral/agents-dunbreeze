# Time de agentes da Dunbreeze no Claude Code

Três agentes, que funcionam em qualquer projeto:

| Agente | Papel | Como usar |
|---|---|---|
| `tech-lead` | Orquestrador: mapeia a stack, planeja, delega e revisa | `claude --agent tech-lead` |
| `tl-mapeador`, `tl-implementador`, `tl-testes`, `tl-revisor` | Subagentes do tech lead | Chamados pelo tech lead |
| `scrum-master` | Requisitos e cards no Linear | "use o scrum-master para quebrar esta reunião em cards" |
| `qa` | Valida cards do Linear: CA, RN, RF e RNF, com evidência | Pelo tech lead, ou `claude --agent qa` para varrer o ciclo |
| `designer` | Propõe telas no Figma, código é a verdade (skills a definir) | "use o designer para propor a tela de login" |

## Instalação (uma vez, vale para todos os projetos)

```bash
mkdir -p ~/.claude/agents
cp agents/*.md ~/.claude/agents/
```

Arquivos em `~/.claude/agents/` valem para qualquer repositório. Se um projeto precisar de uma versão diferente, coloque a cópia em `<repo>/.claude/agents/`, que tem prioridade.

Para o scrum master, conecte o MCP do Linear no Claude Code:

```bash
claude mcp add --transport http linear https://mcp.linear.app/mcp
```

Depois rode `/mcp` dentro do Claude Code para fazer login.

## Por que o tech lead roda como sessão principal

No Claude Code, um subagente não chama outros subagentes. Então o orquestrador precisa ser a sessão principal, e os especialistas são os subagentes. `claude --agent tech-lead` faz exatamente isso. Se a sua versão não tiver essa flag, abra o Claude Code normal e diga "atue como o tech-lead definido em ~/.claude/agents/tech-lead.md".

## Como ele se adapta à stack

Na primeira sessão em cada repositório, o `tl-mapeador` lê manifestos, CI e estrutura e escreve `.claude/stack.md`. Todos os outros agentes leem esse perfil, então ninguém chuta framework ou comando de teste. Vale commitar o `stack.md` no repositório.

## Skills nos agentes

O campo `skills:` no topo de cada arquivo carrega skills no agente assim que ele começa (o `qa` já vem com `break-ui`). Para adicionar outra, inclua o nome dela na lista. Se a sua versão do Claude Code ignorar esse campo, o agente ainda pode usar qualquer skill instalada, porque o `qa` não restringe ferramentas.
