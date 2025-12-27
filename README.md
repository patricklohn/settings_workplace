# 🐧 Ambiente de Desenvolvimento no WSL

Guia completo para configurar um ambiente de desenvolvimento no **WSL (Windows Subsystem for Linux)** com **Git**, **Node.js (via NVM)**, **Yarn**, **backup/restauração** e **integração com o VS Code**.

> 📌 **Observação**: Banco de dados **não** é instalado diretamente no WSL. Para maior eficiência e isolamento, recomenda‑se utilizar **Docker** para subir serviços como PostgreSQL, MySQL, Redis etc.

---

## 📑 Sumário

1. Instalar e configurar o WSL
2. Atualizar pacotes do sistema
3. Instalar e configurar Git
4. Instalar NVM e Node.js
5. Instalar Yarn
6. Criar aliases úteis (ex: `cls`)
7. Backup e restauração do WSL
8. Configurar distro padrão no VS Code
9. Remover uma distro WSL

---

## 🔹 1. Instalar e configurar o WSL

Abra o **PowerShell como Administrador**:

```powershell
wsl --install
wsl --update
```

> ⚠️ Após a instalação inicial, o Windows pode solicitar **reinicialização**.

### 📌 Listar distros instaladas

```powershell
wsl --list --verbose
# ou
wsl -l -v
```

---

## 🔹 2. Atualizar pacotes do sistema

Dentro da distro Linux (ex: Ubuntu):

```bash
sudo apt update && sudo apt upgrade -y
```

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
```

> 💡 **Autenticação**: Para GitHub/GitLab, basta clonar um repositório privado e executar um `git push`. O WSL utiliza o fluxo de autenticação do navegador do Windows.

---

## 🔹 4. Instalar NVM e Node.js

### Instalar o NVM

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/master/install.sh | bash
```

Feche e abra o terminal **ou** carregue manualmente:

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
nvm alias default 20
node -v
npm -v
```

---

## 🔹 5. Instalar Yarn

```bash
npm install -g yarn
yarn -v
```

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

> 💡 Você pode criar outros aliases para comandos repetitivos (Git, Docker, NPM, etc).

---

## 🔹 7. Backup e restauração do WSL

### 📦 Backup (Exportar)

No **PowerShell**:

```powershell
wsl --export Ubuntu C:\Backups\wsl-ubuntu-backup.tar
```

> 📁 Recomenda‑se salvar o backup no disco principal ou em um HD externo.

---

### ♻️ Restaurar (Importar)

```powershell
wsl --import Ubuntu C:\WSL\Ubuntu C:\Backups\wsl-ubuntu-backup.tar
```

> 📌 O diretório `C:\WSL\Ubuntu` será criado automaticamente se não existir.

---

## 🔹 8. Configurar distro padrão no VS Code

### Definir distro padrão no Windows

```powershell
wsl --set-default Ubuntu
```

### Configurar no VS Code (`settings.json`)

```json
"remote.WSL.defaultDistro": "Ubuntu"
```

### Abrir manualmente

* `F1` → **Remote-WSL: New Window**
* Selecione a distro desejada

---

## 🔹 9. Remover uma distro WSL

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

---

📌 **Sugestões futuras**:

* Adicionar Docker + Docker Compose
* Criar scripts de setup automático
* Padronizar ambiente para times
* Versionar este arquivo como `README.md`
