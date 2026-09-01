# 🐧 Ambiente de Desenvolvimento no WSL

Guia completo para configurar um ambiente de desenvolvimento no **WSL (Windows Subsystem for Linux)** com **Git**, **Node.js (via NVM)**, **Yarn** ou **Pnpm**, **backup/restauração** e **integração com o VS Code**.

> 📌 **Observação**: Banco de dados **não** é instalado diretamente no WSL. Para maior eficiência e isolamento, recomenda‑se utilizar **Docker** para subir serviços como PostgreSQL, MySQL, Redis etc.

---

## 📑 Sumário

1. Instalar e configurar o WSL
2. Atualizar pacotes do sistema
3. Instalar e configurar Git
4. Instalar NVM e Node.js
5. Instalar Yarn ou Pnpm
6. Criar aliases úteis (ex: `cls`)
7. Desempenho: onde guardar seus projetos
8. Limitar recursos do WSL (`.wslconfig`)
9. Backup e restauração do WSL
10. Configurar distro padrão no VS Code
11. Docker no WSL
12. Remover uma distro WSL

---

## 📚 Colas de comandos (referência rápida)

Além deste guia de setup, separei os comandos do dia a dia em arquivos próprios — cada um é uma "amansa burro" com os principais comandos, explicados de forma direta:

* 🐧 [`linux-commands.md`](./linux-commands.md) — arquivos, pastas, permissões, processos, memória/disco
* 🌿 [`git-commands.md`](./git-commands.md) — branch, commit, stash, desfazer commit, reset, etc
* 🐳 [`docker-commands.md`](./docker-commands.md) — containers, imagens, volumes, compose, logs

---

## 🔹 1. Instalar e configurar o WSL

Abra o **PowerShell como Administrador**:

```powershell
wsl --install -d Ubuntu
wsl --update
```

> 💡 O parâmetro `-d Ubuntu` deixa explícita a distro instalada. Rode `wsl --list --online` para ver todas as opções disponíveis (Debian, Ubuntu 22.04/24.04, Kali etc).

> ⚠️ Após a instalação inicial, o Windows pode solicitar **reinicialização**.

### 📌 Listar distros instaladas

```powershell
wsl --list --verbose
```
ou
```powershell
wsl -l -v
```

---

## 🔹 2. Atualizar pacotes do sistema

Dentro da distro Linux (ex: Ubuntu):

```bash
sudo apt update && sudo apt upgrade -y
```

> 💡 Vale automatizar isso periodicamente com `sudo apt autoremove -y` para limpar pacotes órfãos.

---

## 🔹 3. Instalar e configurar Git

```bash
sudo apt install git -y
git --version
```

### Configuração inicial

```bash
git config --global user.name "Seu Nome"
git config --global user.email "seuemail@exemplo.com"
git config --global init.defaultBranch main
git config --global pull.rebase false
```

> 💡 **Autenticação HTTPS**: ao clonar via `https://` e rodar `git push`, o Git pode usar o **Git Credential Manager** do Windows (se instalado) para abrir o navegador e autenticar — funciona bem para GitHub/GitLab.
>
> 💡 **Autenticação SSH** (alternativa recomendada para uso frequente): gere uma chave dentro do WSL com `ssh-keygen -t ed25519 -C "seuemail@exemplo.com"` e adicione a chave pública nas configurações do GitHub/GitLab. Evita depender do fluxo do navegador a cada push.

---

## 🔹 4. Instalar NVM e Node.js

### Instalar o NVM

Confira a [versão mais recente do NVM](https://github.com/nvm-sh/nvm/releases) antes de instalar:

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash
```

O script já adiciona as linhas de carregamento ao `~/.bashrc` automaticamente. Feche e abra o terminal **ou** carregue manualmente na sessão atual:

```bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
```

Verificar:

```bash
nvm --version
```

### Instalar Node.js

```bash
nvm install --lts
```

Para fixar a versão LTS instalada como padrão (não fixe "20" às cegas — confira qual foi instalada):

```bash
nvm ls              # veja a versão LTS que acabou de instalar
nvm alias default node   # sempre aponta para a última instalada, ou:
nvm alias default 20     # fixa uma major específica, se preferir
```

Depois, teste a versão instalada:

```bash
node -v
npm -v
```

---

## 🔹 5. Instalar Yarn ou Pnpm

### Opção recomendada: Corepack (vem com o Node ≥ 16.9)

```bash
corepack enable
corepack prepare yarn@stable --activate
# ou
corepack prepare pnpm@latest --activate
```

### Alternativa: instalação global via npm

```bash
npm install -g yarn
yarn -v
```
ou
```bash
npm install -g pnpm
pnpm -v
```

> 💡 O Corepack gerencia a versão do gerenciador de pacotes por projeto (via `packageManager` no `package.json`), evitando divergência de versões entre máquinas/times.

---

## 🔹 6. Criar aliases úteis (ex: `cls`)

Editar o arquivo de configuração do shell:

```bash
nano ~/.bashrc
```
Adicionar ao final do arquivo:
```bash
alias cls='clear'
```
Aplicar as alterações:
```bash
source ~/.bashrc
```

> 💡 Para organizar melhor, crie um arquivo separado `~/.bash_aliases` com todos os seus aliases e garanta que o `.bashrc` o carregue:
> ```bash
> if [ -f ~/.bash_aliases ]; then
>     . ~/.bash_aliases
> fi
> ```

---

## 🔹 7. Desempenho: onde guardar seus projetos

> ⚠️ **Importante**: mantenha seus repositórios dentro do sistema de arquivos **Linux** (ex: `~/projetos/`), **não** em `/mnt/c/...`. Acessar arquivos do Windows (`/mnt/c`) a partir do WSL é significativamente mais lento — isso afeta `git status`, instalação de `node_modules`, build tools, etc.

```bash
mkdir -p ~/projetos
cd ~/projetos
git clone git@github.com:usuario/repo.git
```

No VS Code, abra sempre pelo terminal do WSL (`code .`) estando dentro do diretório Linux, não pelo caminho `\\wsl$` a partir do Explorer do Windows.

---

## 🔹 8. Limitar recursos do WSL (`.wslconfig`)

Por padrão, o WSL2 pode consumir boa parte da RAM/CPU do host. Para limitar, crie/edite o arquivo `%UserProfile%\.wslconfig` no Windows:

```ini
[wsl2]
memory=6GB
processors=4
swap=2GB
```

Depois reinicie o WSL no PowerShell:

```powershell
wsl --shutdown
```

---

## 🔹 9. Backup e restauração do WSL

### 📦 Backup (Exportar)

No **PowerShell**:

```powershell
wsl --export Ubuntu C:\Backups\wsl-ubuntu-backup.tar
```

> 📁 Recomenda‑se salvar o backup no disco principal ou em um HD externo.
>
> 💡 Em versões mais recentes do WSL, é possível exportar em formato VHD (monta mais rápido na restauração):
> ```powershell
> wsl --export Ubuntu C:\Backups\wsl-ubuntu-backup.vhdx --vhd
> ```

---

### ♻️ Restaurar (Importar)

```powershell
wsl --import Ubuntu C:\WSL\Ubuntu C:\Backups\wsl-ubuntu-backup.tar
```

> 📌 O diretório `C:\WSL\Ubuntu` será criado automaticamente se não existir.

---

## 🔹 10. Configurar distro padrão no VS Code

### Definir distro padrão no Windows

```powershell
wsl --set-default Ubuntu
```

### Configurar no VS Code (`settings.json`)

```json
"remote.WSL.defaultDistro": "Ubuntu"
```

> 💡 Instale a extensão **WSL** (`ms-vscode-remote.remote-wsl`) da Microsoft, se ainda não tiver — é ela que habilita `code .` de dentro do WSL.

### Abrir manualmente

* `F1` → **Remote-WSL: New Window**
* Selecione a distro desejada

---

## 🔹 11. Docker no WSL

Existem duas formas de ter Docker funcionando no WSL. Escolha uma delas (não precisa das duas).

### Opção A — Docker Desktop (GUI no Windows + integração)

1. Instale o **Docker Desktop** no Windows.
2. Em **Settings → Resources → WSL Integration**, habilite a integração com sua distro (ex: Ubuntu).
3. Reinicie o Docker Desktop e teste dentro do WSL:
   ```bash
   docker --version
   docker run hello-world
   ```

> 💡 Não precisa instalar o Docker Engine separadamente dentro do WSL — o Docker Desktop já expõe o daemon para as distros integradas. Vantagem: interface gráfica e configuração simples. Desvantagem: consome mais recursos e depende de um app do Windows rodando.

### Opção B — Docker Engine nativo, direto no WSL (via terminal, sem Docker Desktop)

Ideal para quem quer tudo via terminal, sem depender de nenhum app do Windows rodando em segundo plano.

```bash
# Remover versões antigas, se houver
sudo apt remove docker docker-engine docker.io containerd runc -y

# Dependências
sudo apt update
sudo apt install ca-certificates curl gnupg -y

# Adicionar a chave GPG oficial do Docker
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# Adicionar o repositório oficial
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Instalar o Docker Engine + Compose plugin
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
```

Rodar o Docker sem `sudo` (adiciona seu usuário ao grupo `docker`):

```bash
sudo usermod -aG docker $USER
```

> ⚠️ Após esse comando, feche e abra o terminal (ou rode `newgrp docker`) para o grupo ser aplicado.

O WSL2 usa **systemd** por padrão em versões recentes, então o serviço pode subir sozinho no boot. Se não subir automaticamente, inicie manualmente:

```bash
sudo service docker start
```

Para garantir que o systemd está habilitado (necessário para o serviço iniciar sozinho), confira/edite `/etc/wsl.conf`:

```ini
[boot]
systemd=true
```

Depois de editar, reinicie o WSL no PowerShell (`wsl --shutdown`) e abra de novo.

Teste a instalação:

```bash
docker --version
docker compose version
docker run hello-world
```

> 💡 Vantagem da Opção B: mais leve, tudo controlado por terminal, ótimo para quem já vive no WSL. Desvantagem: sem interface gráfica e a configuração inicial é um pouco mais manual.

Comandos do dia a dia (containers, imagens, volumes, compose) estão todos resumidos em [`docker-commands.md`](./docker-commands.md).

---

## 🔹 12. Remover uma distro WSL

No **PowerShell**:

```powershell
wsl --list --verbose
wsl --unregister Ubuntu
```

> ⚠️ Este comando **remove permanentemente** a distro e todos os dados.

---

## ✅ Considerações finais

✔ Ambiente limpo e isolado
✔ Ideal para Node.js, APIs e front‑end
✔ Backup simples e confiável
✔ Integração total com VS Code
✔ Banco de dados via Docker (boa prática)
✔ Projetos no filesystem Linux para performance máxima

---

📌 **Sugestões futuras**:

* Adicionar Docker Compose com exemplos de `docker-compose.yml`
* Criar scripts de setup automático (ex: `setup.sh`)
* Padronizar ambiente para times (incluir `.nvmrc` e `packageManager` nos repositórios)
* Versionar este arquivo como `README.md`
