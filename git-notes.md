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

## Modulo 5 - Repositórios remotos
