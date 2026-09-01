# 🐧 Cola de Comandos Linux

Referência rápida dos comandos mais usados no dia a dia dentro do WSL/Linux. Voltar para o [guia principal](./README.md).

---

## 📑 Sumário

1. Navegação
2. Pastas (criar, remover, mover)
3. Arquivos (criar, editar, remover, copiar)
4. Visualizar conteúdo
5. Permissões
6. Processos
7. Memória e disco
8. Rede
9. Compactação
10. Atalhos e combinações úteis

---

## 🔹 1. Navegação

| Comando | O que faz |
|---|---|
| `pwd` | Mostra o diretório atual (**p**rint **w**orking **d**irectory) |
| `ls` | Lista arquivos e pastas |
| `ls -la` | Lista tudo, incluindo ocultos, em formato detalhado |
| `cd pasta/` | Entra na pasta |
| `cd ..` | Sobe um nível |
| `cd ~` ou `cd` | Vai para o diretório home |
| `cd -` | Volta para o diretório anterior |

---

## 🔹 2. Pastas (criar, remover, mover)

```bash
mkdir minha-pasta            # cria uma pasta
mkdir -p a/b/c                # cria pastas aninhadas de uma vez (cria a, depois b dentro, depois c dentro)
rmdir minha-pasta             # remove pasta VAZIA
rm -r minha-pasta             # remove pasta com conteúdo
rm -rf minha-pasta            # remove sem pedir confirmação (⚠️ cuidado, é irreversível)
mv pasta-antiga nova-pasta    # renomeia (ou move, se o destino for outro caminho)
```

> ⚠️ `rm -rf` não vai para lixeira — a exclusão é definitiva. Confira o caminho com `pwd` antes de rodar.

---

## 🔹 3. Arquivos (criar, editar, remover, copiar)

```bash
touch arquivo.txt             # cria um arquivo vazio (ou atualiza a data de modificação se já existir)
nano arquivo.txt               # abre editor simples de terminal
code arquivo.txt                # abre no VS Code (se instalado e integrado ao WSL)
rm arquivo.txt                  # remove o arquivo
cp origem.txt destino.txt       # copia
cp -r pasta-origem pasta-destino  # copia pasta inteira
mv origem.txt destino.txt       # move ou renomeia
```

### Editor nano — atalhos essenciais
| Atalho | Ação |
|---|---|
| `Ctrl + O` | Salvar |
| `Ctrl + X` | Sair |
| `Ctrl + K` | Recortar linha |
| `Ctrl + U` | Colar linha |

---

## 🔹 4. Visualizar conteúdo

```bash
cat arquivo.txt        # mostra o conteúdo inteiro no terminal
less arquivo.txt        # mostra com rolagem (sai com "q")
head arquivo.txt         # mostra as primeiras 10 linhas
head -n 20 arquivo.txt    # mostra as primeiras 20 linhas
tail arquivo.txt          # mostra as últimas 10 linhas
tail -f arquivo.log        # acompanha um log em tempo real (ótimo para debugar)
grep "erro" arquivo.log     # busca a palavra "erro" no arquivo
grep -r "TODO" .             # busca "TODO" recursivamente em todos os arquivos da pasta atual
```

---

## 🔹 5. Permissões

```bash
ls -l arquivo.txt              # mostra as permissões atuais
chmod +x script.sh              # torna o arquivo executável
chmod 644 arquivo.txt            # define permissão rw-r--r--
chmod -R 755 pasta/               # aplica permissão recursivamente
chown usuario:grupo arquivo.txt    # muda o dono do arquivo
sudo comando                        # executa como superusuário (root)
```

> 💡 Leitura rápida de permissões: `rwx rwx rwx` = dono / grupo / outros, cada trio sendo **r**ead, **w**rite, e**x**ecute.

---

## 🔹 6. Processos

```bash
ps aux                  # lista todos os processos rodando
top                       # monitor de processos em tempo real (sai com "q")
htop                       # versão mais amigável do top (instalar com: sudo apt install htop)
kill 1234                   # encerra o processo de PID 1234
kill -9 1234                 # força o encerramento (⚠️ use como último recurso)
pkill nome-do-processo         # encerra pelo nome, sem precisar do PID
```

---

## 🔹 7. Memória e disco

```bash
free -h                  # memória RAM disponível/usada (formato legível)
df -h                      # espaço em disco por partição/ponto de montagem
du -sh pasta/                # tamanho total de uma pasta
du -sh */                     # tamanho de cada subpasta do diretório atual
```

---

## 🔹 8. Rede

```bash
ip a                     # mostra os endereços IP das interfaces de rede
ping google.com            # testa conectividade
curl https://exemplo.com     # faz uma requisição HTTP e mostra a resposta
curl -I https://exemplo.com   # mostra só os headers da resposta
wget https://exemplo.com/arquivo.zip   # baixa um arquivo
```

---

## 🔹 9. Compactação

```bash
tar -czvf backup.tar.gz pasta/     # compacta uma pasta em .tar.gz
tar -xzvf backup.tar.gz              # extrai um .tar.gz
zip -r backup.zip pasta/               # compacta em .zip
unzip backup.zip                         # extrai um .zip
```

---

## 🔹 10. Atalhos e combinações úteis

```bash
comando1 && comando2      # roda comando2 SOMENTE SE comando1 der certo
comando1 ; comando2         # roda comando2 independente do resultado do comando1
comando > saida.txt           # salva a saída do comando em um arquivo (sobrescreve)
comando >> saida.txt            # adiciona a saída ao final do arquivo
comando1 | comando2               # envia a saída do comando1 como entrada do comando2 (pipe)
history                             # mostra o histórico de comandos digitados
!!                                    # repete o último comando
clear                                  # limpa a tela do terminal
```

### Atalhos de teclado no terminal
| Atalho | Ação |
|---|---|
| `Ctrl + C` | Cancela o comando em execução |
| `Ctrl + L` | Limpa a tela (igual `clear`) |
| `Ctrl + R` | Busca no histórico de comandos |
| `Ctrl + A` | Vai para o início da linha |
| `Ctrl + E` | Vai para o final da linha |
| `Tab` | Autocompleta comandos/caminhos |
