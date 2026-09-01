# 🌿 Cola de Comandos Git

Referência rápida dos comandos Git mais usados no dia a dia. Voltar para o [guia principal](./README-wsl-dev-environment.md).

---

## 📑 Sumário

1. Configuração e criação de repositório
2. Status e histórico
3. Branches
4. Commits
5. Desfazer coisas (reset, revert, amend)
6. Stash
7. Remotos (push, pull, fetch)
8. Merge e rebase
9. Tags
10. Situações comuns do dia a dia

---

## 🔹 1. Configuração e criação de repositório

```bash
git init                          # cria um repositório Git na pasta atual
git clone <url>                     # clona um repositório existente
git clone <url> pasta-destino         # clona para uma pasta com nome específico
git config --global user.name "Nome"    # define seu nome (uma vez por máquina)
git config --global user.email "email"    # define seu e-mail (uma vez por máquina)
```

---

## 🔹 2. Status e histórico

```bash
git status                 # mostra o que mudou (staged, não-staged, não rastreado)
git log                      # histórico de commits completo
git log --oneline              # histórico resumido, uma linha por commit
git log --oneline --graph --all  # histórico visual com branches
git diff                          # mostra as diferenças não commitadas
git diff --staged                   # mostra as diferenças já no stage
git show <hash>                       # mostra detalhes de um commit específico
```

---

## 🔹 3. Branches

```bash
git branch                       # lista as branches locais (a atual vem marcada com *)
git branch -a                      # lista branches locais + remotas
git branch nome-da-branch            # cria uma branch nova (sem mudar para ela)
git checkout nome-da-branch            # muda para a branch
git checkout -b nome-da-branch           # cria E já muda para a branch nova (atalho mais usado)
git switch nome-da-branch                  # forma mais moderna de trocar de branch
git switch -c nome-da-branch                 # forma moderna de criar + trocar
git branch -d nome-da-branch                   # remove uma branch já mesclada
git branch -D nome-da-branch                     # força a remoção, mesmo sem merge
git branch -m novo-nome                            # renomeia a branch atual
```

---

## 🔹 4. Commits

```bash
git add arquivo.txt              # adiciona um arquivo específico ao stage
git add .                          # adiciona TODOS os arquivos modificados ao stage
git add -p                           # adiciona interativamente, por trecho (bom para commits organizados)
git commit -m "mensagem"               # cria um commit com o que está no stage
git commit -am "mensagem"                # adiciona (só arquivos já rastreados) + commita em um passo
```

> 💡 Boas mensagens de commit costumam seguir um padrão, ex: `feat: adiciona login`, `fix: corrige bug no cálculo`, `docs: atualiza README`.

---

## 🔹 5. Desfazer coisas (reset, revert, amend)

```bash
git restore arquivo.txt                # descarta mudanças não commitadas em um arquivo
git restore --staged arquivo.txt         # tira o arquivo do stage (sem perder a mudança)

git commit --amend -m "nova mensagem"      # corrige a mensagem do ÚLTIMO commit
git commit --amend --no-edit                 # adiciona mudanças esquecidas ao último commit, sem mudar a mensagem

git reset --soft HEAD~1        # desfaz o último commit, mantendo as mudanças no stage
git reset --mixed HEAD~1         # desfaz o último commit, mantendo as mudanças (fora do stage) — padrão
git reset --hard HEAD~1            # ⚠️ desfaz o último commit E APAGA as mudanças (irreversível)

git revert <hash>                     # cria um novo commit que desfaz um commit específico (seguro para branches compartilhadas)
```

> ⚠️ `reset --hard` reescreve o histórico e descarta mudanças de verdade. Evite usar em branches já compartilhadas com o time — prefira `revert`.

---

## 🔹 6. Stash

Guarda mudanças não commitadas "de lado" temporariamente, sem precisar commitar.

```bash
git stash                     # guarda as mudanças atuais e limpa o diretório de trabalho
git stash -u                    # inclui também arquivos não rastreados (untracked)
git stash list                    # lista todos os stashes salvos
git stash pop                       # aplica o último stash E o remove da lista
git stash apply                       # aplica o último stash, mas MANTÉM na lista
git stash apply stash@{2}                # aplica um stash específico da lista
git stash drop stash@{0}                   # remove um stash específico sem aplicar
git stash clear                              # remove todos os stashes
```

---

## 🔹 7. Remotos (push, pull, fetch)

```bash
git remote -v                    # lista os remotos configurados
git remote add origin <url>        # adiciona um remoto chamado "origin"

git push                             # envia commits para o remoto (branch já configurada)
git push -u origin nome-da-branch      # envia e já vincula a branch local à remota (primeira vez)
git push --force-with-lease              # sobrescreve o remoto de forma mais segura que --force

git pull                                   # busca e já mescla as mudanças do remoto
git pull --rebase                            # busca e reaplica seus commits por cima (histórico mais limpo)

git fetch                                      # busca as mudanças do remoto SEM mesclar automaticamente
```

---

## 🔹 8. Merge e rebase

```bash
git checkout main
git merge nome-da-branch          # mescla a branch na branch atual (main), criando um commit de merge

git checkout nome-da-branch
git rebase main                     # reaplica os commits da branch atual sobre a main (histórico linear)

git rebase -i HEAD~3                  # rebase interativo — reorganizar/juntar/editar os últimos 3 commits
```

> 💡 Regra prática: `merge` preserva o histórico real (com "nós"); `rebase` deixa o histórico linear, mas reescreve commits — evite rebase em branches que outras pessoas já usam.

---

## 🔹 9. Tags

```bash
git tag                           # lista as tags
git tag v1.0.0                      # cria uma tag simples no commit atual
git tag -a v1.0.0 -m "Versão 1.0"     # cria uma tag anotada (com mensagem)
git push origin v1.0.0                  # envia uma tag específica para o remoto
git push origin --tags                    # envia todas as tags
```

---

## 🔹 10. Situações comuns do dia a dia

**"Commitei na branch errada"**
```bash
git reset --soft HEAD~1     # desfaz o commit mantendo as mudanças
git switch branch-correta
git commit -m "mensagem"      # commita na branch certa
```

**"Quero descartar TODAS as mudanças locais e voltar ao último commit"**
```bash
git reset --hard HEAD
git clean -fd     # remove também arquivos/pastas não rastreados
```

**"Preciso trocar de branch mas tenho mudanças no meio do caminho"**
```bash
git stash
git switch outra-branch
# ... faz o que precisa ...
git switch branch-original
git stash pop
```

**"Quero ver quem mudou uma linha específica de um arquivo"**
```bash
git blame arquivo.txt
```

**"Apaguei um commit sem querer, dá pra recuperar?"**
```bash
git reflog             # mostra TODAS as ações recentes, mesmo commits "perdidos"
git checkout <hash>       # volta para o commit encontrado no reflog
```
