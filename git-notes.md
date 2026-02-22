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
