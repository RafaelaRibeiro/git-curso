# Git - Caderno de Referência

## Módulo 1 — Fundamentos

### Conceitos
- **Working Directory** — arquivos locais do projeto
- **Staging Area (Index)** — arquivos preparados para o próximo commit
- **Repository (.git)** — histórico de commits salvo

> O controle do que entra no commit acontece na hora do `git add`, não do `git commit`.


### Configuração inicial
```bash
git config --global user.name "Seu Nome"
git config --global user.email "seu@email.com"
git config --list                  # confirma as configurações
```

### Criar repositório
```bash
git init nome-do-projeto           # cria pasta + repositório
git init                           # inicializa na pasta atual
```

## Módulo 2 — O ciclo de vida de um arquivo

### Estados de um arquivo
- **Untracked** -  o Git vê o arquivo existindo, mas nunca foi instruído a rastreá-lo. Arquivos novos começam aqui.
- **Staged** - você disse ao Git "esse arquivo entra no próximo commit". Ele está na Staging Area.
- **Committed** - o snapshot está salvo no repositório. O Working Directory está limpo em relação ao último commit.
- **Modified** - o arquivo já foi commitado antes, mas você fez mudanças desde então. Ainda não está na Staging.


### Comandos
```bash
git status                        # estado atual do repositório
git add arquivo.txt               # adiciona arquivo específico à Staging
git add .                         # adiciona tudo (cuidado: revise antes)
git add -p arquivo.txt            # adiciona por trecho (hunk) — preciso e poderoso
git commit -m "mensagem"          # commita o que está na Staging
git commit -am "mensagem"         # add + commit (só arquivos já rastreados)
git log                           # histórico completo
git log --oneline                 # histórico compacto
git log --oneline --graph --all   # histórico com gráfico de branches
```

## Módulo 3 — Branches

### Conceitos
- Branch é um arquivo de 40 bytes com um hash de commit — por isso é barata
- HEAD aponta para a branch atual, que aponta para o último commit
- Commitar avança a branch automaticamente; HEAD acompanha

### Comandos
```bash
git branch                        # lista branches (* = atual)
git branch nome                   # cria branch (não muda para ela)
git switch nome                   # muda para a branch
git switch -c nome                # cria e já muda
git branch -d nome                # deleta branch mergeada
git branch -D nome                # força deleção
git log --oneline --graph --all   # visualiza divergência entre branches
```

### Prefixos comuns
- feature/  fix/  refactor/  chore/


## Módulo 4 — Merge e Rebase

### Conceitos
- **Merge** — une históricos preservando a divergência, cria commit com dois pais
- **Fast-forward** — merge sem commit extra quando a base não avançou
- **Rebase** — reaplica commits em cima de outro ponto, histórico linear, hashes mudam
- Conflito acontece nos dois — resolução é igual: editar, `git add`, continuar

### Quando usar
| Situação | Estratégia |
|---|---|
| Integrar feature finalizada | Merge |
| Atualizar branch local com master | Rebase |
| Branch já compartilhada | Merge (nunca rebase) |

### Comandos
```bash
git merge branch              # merge na branch atual
git merge --no-ff branch      # força commit de merge mesmo em fast-forward
git merge --abort             # aborta merge em andamento

git rebase master             # reaplica commits da branch atual em cima de master
git rebase --continue         # continua após resolver conflito
git rebase --abort            # aborta e volta ao estado anterior

# Resolução de conflito (vale para merge e rebase)
# 1. Editar arquivo, remover marcadores <<<<< ===== >>>>>
git add arquivo               # marca como resolvido
git commit                    # finaliza merge
git rebase --continue         # finaliza rebase
```

> **Regra de ouro:** nunca faça rebase de commits já enviados para repositório compartilhado.

## Módulo 5 — Repositórios Remotos

### Conceitos
- **Remoto** — referência para outro repositório Git (GitHub, GitLab, etc)
- **origin** — nome convencional para o remoto principal
- **origin/master** — remote tracking branch: snapshot do remoto na última comunicação
- `master` e `origin/master` são coisas diferentes — ficam dessincronizadas normalmente

### Comandos
```bash
git remote -v                     # lista remotos configurados
git remote add origin <url>       # conecta repo local a um remoto
git clone <url>                   # clona remoto (já configura origin)

git push -u origin master         # primeiro push + configura tracking
git push                          # envia commits (após tracking configurado)

git fetch origin                  # baixa objetos e atualiza origin/*, não toca em branches locais
git pull                          # fetch + merge automático
git pull --rebase                 # fetch + rebase (histórico mais limpo)

git branch -a                     # lista branches locais e remotas

# Fluxo seguro do dia a dia
git fetch origin
git log --oneline --graph --all   # avalia o que chegou
git merge origin/master           # integra quando pronto
git push                          # envia
```

> **Cuidado:** `git push --force` sobrescreve histórico remoto. Prefira `--force-with-lease`.
> **Prefer fetch + merge** ao `git pull` — te dá controle sobre o que está integrando.

## Módulo 6 — Desfazendo Coisas

### Mapa dos comandos por área
- Working Directory → git restore arquivo
- Staging Area      → git restore --staged arquivo
- Repository        → git revert / git reset

### Comandos
```bash
# Working Directory e Staging
git restore arquivo.txt              # descarta mudanças locais (irreversível)
git restore --staged arquivo.txt     # tira da Staging, mantém mudanças locais

# Histórico — seguro para branches compartilhadas
git revert HEAD                      # reverte último commit (cria commit novo)
git revert b54dc61                   # reverte commit específico
git revert --no-commit b54dc61       # aplica reversão sem commitar

# Histórico — apenas branches locais não publicadas
git reset --soft HEAD~1              # desfaz commit, mudanças voltam para Staging
git reset --mixed HEAD~1             # desfaz commit, mudanças voltam para Modified
git reset --hard HEAD~1              # desfaz commit, descarta tudo ⚠️

# Corrigir último commit
git commit --amend -m "mensagem"     # corrige mensagem
git commit --amend --no-edit         # adiciona arquivo esquecido
```

### Quando usar
| Situação | Comando |
|---|---|
| Commit não publicado, reformular | reset --soft |
| Commit não publicado, descartar tudo | reset --hard |
| Commit já publicado | revert |
| Esqueceu arquivo ou errou mensagem | commit --amend |

> Reset reescreve histórico — nunca use em commits já publicados.

## Módulo 7 — Stash

### Conceito
Guarda trabalho inacabado em uma pilha temporária e limpa o Working Directory.
Não é substituto de commit — use para pausas curtas de contexto.

### Comandos
```bash
git stash                          # guarda Working Directory e Staging
git stash push -m "descrição"      # guarda com mensagem descritiva
git stash push -u -m "descrição"   # inclui arquivos untracked
git stash list                     # lista stashes guardados
git stash pop                      # restaura e remove da pilha
git stash pop stash@{1}            # restaura stash específico
git stash apply                    # restaura e mantém na pilha
git stash drop                     # remove da pilha sem restaurar
git stash clear                    # remove todos os stashes
git stash branch feature/nome      # cria branch a partir do stash
```

### pop vs apply
- **pop** — restaura e remove. Uso padrão.
- **apply** — restaura e mantém. Usar quando o mesmo stash será aplicado em mais de uma branch.

> Stash não vai para o remoto. Para pausas longas, prefira um commit `wip:` numa branch de trabalho.

## Módulo 8 — Fluxos de Trabalho

### Git Flow — releases versionadas, ciclos definidos
- `main` — produção, recebe merge de release e hotfix
- `develop` — integração das features
- `feature/*` — parte de develop, volta para develop
- `hotfix/*` — parte de main, volta para main e develop

### Modelo simplificado (sprints + staging)
- `main` — produção
- `develop` — integração, reflete staging
- `feature/*` — uma por tarefa
- `hotfix/*` — correções urgentes

### Trunk-Based Development
- Todos commitam em main diretamente
- Branches de vida curta (horas/1-2 dias)
- Viabilizado por feature flags e CI/CD maduro

### Conventional Commits
```
tipo(escopo): descrição no imperativo, máx 72 chars

corpo opcional: explica o PORQUÊ, não o quê
```

| Tipo | Uso |
|---|---|
| feat | nova funcionalidade |
| fix | correção de bug |
| refactor | sem alterar comportamento |
| docs | documentação |
| chore | config, dependências |
| test | testes |
| perf | performance |

### Regras de mensagem
- Imperativo: "adiciona", não "adicionado"
- Título máx 72 caracteres
- Corpo explica o porquê — o diff já mostra o quê
- Um commit, uma responsabilidade
## Módulo 9 — Ferramentas Avançadas

### git reflog — rede de segurança local
```bash
git reflog                        # histórico completo do HEAD
git reset --hard HEAD@{2}         # volta para estado anterior
git checkout -b recuperado HEAD@{2} # recupera commit "perdido"
```
> Reflog é local e expira em 90 dias. Antes de entrar em pânico, consulte o reflog.

### git cherry-pick — trazendo commits específicos
```bash
git cherry-pick b54dc61           # aplica commit na branch atual
git cherry-pick a3f..f4d          # sequência de commits
git cherry-pick --no-commit hash  # aplica sem commitar
```
> Gera hash novo — mesmo conteúdo, novo contexto. Pode gerar conflito no merge futuro.

### git bisect — busca binária por regressões
```bash
git bisect start
git bisect bad                    # HEAD tem o bug
git bisect good e72fe34           # esse commit estava ok
git bisect good / bad             # repete até identificar o commit
git bisect reset                  # volta ao HEAD
git bisect run npm test           # automatiza com script
```

### git tag — marcando releases
```bash
git tag -a v1.0.0 -m "mensagem"   # tag anotada (use para releases)
git tag v1.0.0                    # tag leve (sem metadados)
git tag -a v1.0.0 hash -m "msg"   # tag em commit específico
git show v1.0.0                   # detalhes da tag
git push origin v1.0.0            # envia tag para remoto
git push origin --tags            # envia todas as tags
```

### git log avançado
```bash
git log --author="nome"           # por autor
git log --grep="termo"            # por mensagem
git log -- arquivo.ts             # por arquivo
git log --after="2024-01-01"      # por data
git log -p                        # com diff
git log --stat                    # resumo de linhas alteradas
```
## Módulo 10 — Git no Dia a Dia Profissional

### .gitignore
```bash
node_modules/
dist/
.env
.env.*
!.env.example
*.log
.DS_Store
```
```bash
git rm --cached arquivo.env       # para de rastrear sem deletar localmente
git config --global core.excludesfile ~/.gitignore_global  # gitignore global
```

### Aliases
```bash
git config --global alias.lg      "log --oneline --graph --all"
git config --global alias.st      "status"
git config --global alias.sw      "switch"
git config --global alias.undo    "reset --soft HEAD~1"
git config --global alias.aliases "config --get-regexp alias"
```

### Hooks
```bash
# Localização
.git/hooks/pre-commit             # roda antes do commit (lint)
.git/hooks/commit-msg             # valida mensagem
.git/hooks/pre-push               # roda antes do push (testes)

# Compartilhar hooks com o time
git config core.hooksPath .githooks
```

### Comandos úteis
```bash
git blame arquivo.ts              # autoria linha a linha
git blame -L 10,25 arquivo.ts     # intervalo de linhas
git diff                          # Working Directory vs Staging
git diff --staged                 # Staging vs último commit
git diff master..feature/login    # entre branches
git diff v1.0.0..v1.1.0           # entre tags
```

### Configurações recomendadas
```bash
git config --global pull.rebase true
git config --global help.autocorrect 20
git config --global color.ui auto
git config --global init.defaultBranch main
```

---

## Referência — Conventional Commits

### Estrutura
```
tipo(escopo): descrição no imperativo, máx 72 chars

corpo opcional: explica o PORQUÊ
```

### Exemplos por tipo

**feat** — nova funcionalidade
```
feat(auth): implementa refresh token rotation
feat(client): adiciona repositório com operações CRUD
feat(user): adiciona validação de senha obrigatória no cadastro
```

**fix** — correção de bug
```
fix(frete): corrige cálculo quando peso é fracionado
fix(shipper): corrige deleção que não removia registros dependentes
fix(auth): corrige token expirado não redirecionando para login
```

**refactor** — sem alterar comportamento externo
```
refactor(container): extrai lógica de validação para service
refactor(mapper): simplifica conversão de entidade para DTO
```

**docs** — documentação
```
docs(readme): adiciona instruções de setup do ambiente
docs(api): documenta endpoints de autenticação
```

**chore** — configuração, dependências, build
```
chore: atualiza dependências do NestJS para v10
chore(docker): adiciona configuração para ambiente de staging
```

**test** — testes
```
test(auth): adiciona testes unitários para validação de token
test(frete): cobre cenários de peso fracionado
```

**perf** — performance
```
perf(query): adiciona índice em shipper_id na tabela de pedidos
```
