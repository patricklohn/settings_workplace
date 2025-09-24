# 🐧 Ambiente de Desenvolvimento no WSL + PostgreSQL

Guia para configurar um ambiente de desenvolvimento no **WSL** com **Git**, **Node (via NVM)**, **Yarn**, **PostgreSQL**, incluindo **backup** e **integração com o VS Code**.

---

## 📑 Sumário
1. Instalar e Configurar WSL
2. Atualizar pacotes
3. Instalar Git
4. Instalar NVM e Node.js
5. Instalar Yarn
6. Instalar PostgreSQL (mesma distro)
7. Criar distro dedicada para PostgreSQL
8. Backup do Ambiente
9. Configurar distro default no VS Code

---

## 🔹 1. Instalar e Configurar WSL
No PowerShell (como administrador):
*  wsl --install
*  wsl --update

### Extra: 
Comando uteis para listar distros wsl
* wsl --list --verbose
* wsl -l -v

## 🔹 2. Atualizar pacotes
sudo apt update && sudo apt upgrade -y

---

## 🔹 3. Instalar Git
*  sudo apt install git -y
*  git --version

Configuração inicial:
*  git config --global user.name "Seu Nome"
*  git config --global user.email "seuemail@exemplo.com"

---

## 🔹 4. Instalar NVM e Node.js
Instalar NVM:
*  curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/master/install.sh | bash

Ativar:
*  export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"

Verificar:
*  nvm --version

Instalar Node:
*  nvm install --lts
*  nvm alias default 20   # define versão padrão

---

## 🔹 5. Instalar Yarn
Via npm:
*  npm install -g yarn

Verificar:
*  yarn -v

---

## 🔹 6. Instalar PostgreSQL (mesma distro)
Instalar:
sudo apt update
sudo apt install postgresql postgresql-contrib -y

Iniciar serviço:
sudo service postgresql start

Acessar como usuário postgres:
sudo -i -u postgres
psql

Criar usuário e banco para desenvolvimento:
CREATE USER dev WITH PASSWORD 'senha_segura';
ALTER USER dev CREATEDB;
CREATE DATABASE devdb OWNER dev;
GRANT ALL PRIVILEGES ON DATABASE devdb TO dev;

Sair:
\q

Testar login:
psql -U dev -d devdb

---

## 🔹 7. Criar distro dedicada para PostgreSQL
Criar nova distro no Windows:
wsl --install -d Ubuntu-Postgres

Abrir:
wsl -d Ubuntu-Postgres

Instalar PostgreSQL na nova distro:
sudo apt update
sudo apt install postgresql postgresql-contrib -y
sudo service postgresql start

Configuração inicial igual ao passo anterior.

### Acessando PostgreSQL de outra distro ou host
1. Descobrir o IP da distro onde o PostgreSQL está:
hostname -I

2. Editar postgresql.conf:
sudo nano /etc/postgresql/14/main/postgresql.conf
listen_addresses = '*'

3. Editar pg_hba.conf:
sudo nano /etc/postgresql/14/main/pg_hba.conf
Adicionar:
host    all             all             0.0.0.0/0               md5

4. Reiniciar serviço:
sudo service postgresql restart

5. Conectar de outra distro ou do host:
psql -h <IP> -U dev -d devdb

---

## 🔹 8. Backup do Ambiente

### Backup completo do WSL
Exportar:
wsl --export Ubuntu D:\Backups\wsl-ubuntu-backup.tar

Importar:
wsl --import Ubuntu D:\WSL\Ubuntu D:\Backups\wsl-ubuntu-backup.tar

### Backup de pacotes e configs
Exportar pacotes:
dpkg --get-selections > packages.list

Restaurar:
sudo apt update
sudo dpkg --set-selections < packages.list
sudo apt-get dselect-upgrade -y

Exportar Node:
nvm list > nvm-backup.txt

Exportar Yarn:
yarn global list --depth=0 > yarn-global.txt

### Backup do PostgreSQL
Exportar banco:
pg_dump -U dev -d devdb > backup.sql

Restaurar:
psql -U dev -d devdb < backup.sql

---

## 🔹 9. Configurar distro default no VS Code

No Windows:
wsl --list --verbose
wsl --set-default Ubuntu

No VS Code (settings.json):
"remote.WSL.defaultDistro": "Ubuntu"

Abrir manualmente:
F1 → Remote-WSL: New Window → escolha a distro.
