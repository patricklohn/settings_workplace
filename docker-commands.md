# 🐳 Cola de Comandos Docker

Referência rápida dos comandos Docker mais usados no dia a dia via terminal. Voltar para o [guia principal](./README.md).

---

## 📑 Sumário

1. Conceitos rápidos
2. Imagens
3. Containers — criar e rodar
4. Containers — gerenciar
5. Logs e acesso ao container
6. Volumes (persistência de dados)
7. Redes
8. Docker Compose
9. Limpeza do sistema
10. Situações comuns do dia a dia

---

## 🔹 1. Conceitos rápidos

* **Imagem**: o "molde" (ex: `postgres`, `node`, `nginx`) — não roda sozinha.
* **Container**: uma instância em execução de uma imagem.
* **Volume**: armazenamento persistente, sobrevive mesmo se o container for removido.
* **Rede**: permite containers se comunicarem entre si pelo nome.

---

## 🔹 2. Imagens

```bash
docker images                    # lista as imagens baixadas localmente
docker pull postgres:16             # baixa uma imagem específica (sem rodar)
docker rmi postgres:16                # remove uma imagem
docker build -t minha-app .             # cria uma imagem a partir de um Dockerfile na pasta atual
docker build -t minha-app:1.0 .           # cria com uma tag de versão específica
docker tag minha-app minha-app:latest       # cria um "apelido"/tag para uma imagem existente
```

---

## 🔹 3. Containers — criar e rodar

```bash
docker run postgres                 # roda um container em primeiro plano (trava o terminal)
docker run -d postgres                # roda em segundo plano (detached) — o mais usado no dia a dia
docker run -d --name meu-postgres postgres    # roda com um nome fácil de lembrar
docker run -d -p 5432:5432 postgres             # mapeia a porta do host:container
docker run -d -e POSTGRES_PASSWORD=123456 postgres   # define variável de ambiente
docker run -d -v meus-dados:/var/lib/postgresql/data postgres  # usa um volume nomeado
docker run -it ubuntu bash            # roda interativo, abrindo um terminal dentro do container
```

> 💡 Combinação comum para subir um banco rapidamente:
> ```bash
> docker run -d \
>   --name meu-postgres \
>   -e POSTGRES_PASSWORD=123456 \
>   -p 5432:5432 \
>   -v meus-dados:/var/lib/postgresql/data \
>   postgres:16
> ```

---

## 🔹 4. Containers — gerenciar

```bash
docker ps                    # lista containers RODANDO
docker ps -a                   # lista TODOS os containers (parados também)
docker start meu-postgres        # inicia um container parado
docker stop meu-postgres           # para um container rodando (envia sinal, aguarda encerrar)
docker restart meu-postgres          # reinicia
docker kill meu-postgres               # força o encerramento imediato
docker rm meu-postgres                   # remove um container parado
docker rm -f meu-postgres                  # força a remoção mesmo rodando
docker rename antigo-nome novo-nome          # renomeia um container
```

---

## 🔹 5. Logs e acesso ao container

```bash
docker logs meu-postgres              # mostra os logs do container
docker logs -f meu-postgres             # acompanha os logs em tempo real (igual "tail -f")
docker logs --tail 100 meu-postgres       # mostra só as últimas 100 linhas

docker exec -it meu-postgres bash           # abre um terminal DENTRO do container já rodando
docker exec -it meu-postgres psql -U postgres  # roda um comando específico dentro do container
docker inspect meu-postgres                       # detalhes completos (IP, configs, mounts) em JSON
```

---

## 🔹 6. Volumes (persistência de dados)

```bash
docker volume ls                    # lista os volumes
docker volume create meus-dados       # cria um volume nomeado
docker volume inspect meus-dados        # detalhes de um volume (onde fica no disco, etc)
docker volume rm meus-dados               # remove um volume
docker volume prune                         # remove TODOS os volumes não usados por nenhum container
```

> 💡 Sem `-v`, os dados do container somem quando ele é removido. Sempre use volume para bancos de dados.

---

## 🔹 7. Redes

```bash
docker network ls                        # lista as redes
docker network create minha-rede            # cria uma rede customizada
docker run -d --network minha-rede --name api minha-app  # coloca o container numa rede específica
docker network inspect minha-rede               # detalhes da rede (containers conectados, etc)
```

> 💡 Containers na mesma rede customizada conseguem se comunicar pelo **nome do container** como se fosse um hostname (ex: a API acessa o banco em `meu-postgres:5432`).

---

## 🔹 8. Docker Compose

Para orquestrar múltiplos containers com um único arquivo `docker-compose.yml`.

```bash
docker compose up                # sobe os serviços definidos (primeiro plano)
docker compose up -d               # sobe em segundo plano
docker compose down                  # para e REMOVE os containers/redes criados pelo compose
docker compose down -v                 # também remove os volumes (⚠️ apaga dados)
docker compose ps                        # lista os containers do projeto atual
docker compose logs -f                     # acompanha os logs de todos os serviços
docker compose logs -f nome-servico          # acompanha os logs de um serviço específico
docker compose build                           # rebuilda as imagens definidas no compose
docker compose restart nome-servico              # reinicia um serviço específico
docker compose exec nome-servico bash              # abre terminal dentro de um serviço rodando
```

Exemplo básico de `docker-compose.yml`:

```yaml
services:
  db:
    image: postgres:16
    environment:
      POSTGRES_PASSWORD: 123456
    ports:
      - "5432:5432"
    volumes:
      - dados-db:/var/lib/postgresql/data

volumes:
  dados-db:
```

---

## 🔹 9. Limpeza do sistema

```bash
docker container prune       # remove todos os containers parados
docker image prune              # remove imagens "soltas" (sem tag/uso)
docker image prune -a             # remove TODAS as imagens não usadas por nenhum container
docker volume prune                 # remove volumes não usados
docker network prune                  # remove redes não usadas
docker system prune                     # limpeza geral (containers parados, redes e imagens soltas)
docker system prune -a --volumes          # limpeza AGRESSIVA — remove praticamente tudo que não está em uso (⚠️ cuidado)
docker system df                            # mostra quanto espaço em disco o Docker está usando
```

---

## 🔹 10. Situações comuns do dia a dia

**"A porta já está em uso"**
```bash
docker ps                       # veja qual container já está usando a porta
docker stop <nome-ou-id>           # pare ele, ou mude a porta do novo container (-p 5433:5432)
```

**"Preciso ver o IP interno de um container"**
```bash
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' meu-postgres
```

**"O container fica reiniciando sozinho (crash loop)"**
```bash
docker logs meu-postgres     # veja o motivo nos logs primeiro
```

**"Quero garantir que o container suba sempre que o Docker iniciar"**
```bash
docker run -d --restart unless-stopped --name meu-postgres postgres
```

**"Container travou e não responde ao stop"**
```bash
docker kill meu-postgres
```

**"Quero rodar um comando único e já sair (sem manter o container)"**
```bash
docker run --rm -it node:20 node -v
```
> `--rm` remove o container automaticamente assim que ele termina.
