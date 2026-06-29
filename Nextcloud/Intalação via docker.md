
# Nextcloud via Docker 


## Pré-requisitos

Antes de começar:

- Docker instalado
- Docker Compose funcionando
- Porta 8080 livre

Testa rápido:

```bash
docker --version
docker compose version
````

---

## 1. Estrutura do projeto

Cria a pasta do projeto:

```bash
mkdir -p ~/docker/nextcloud/{app,db}
cd ~/docker/nextcloud
```

O que acontece aqui:

* `app/` guarda os dados do Nextcloud
* `db/` guarda o banco
* tudo fica isolado nesse projeto

---

## 2. Criar o docker-compose.yml

```bash
nano docker-compose.yml
```

Cole:

```yaml
services:
  db:
    image: mariadb:10.6
    container_name: nextcloud_db
    restart: always
    volumes:
      - ./db:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: "SENHA_ROOT"
      MYSQL_PASSWORD: "SENHA_APP"
      MYSQL_DATABASE: nextcloud
      MYSQL_USER: nextcloud

  app:
    image: nextcloud:latest
    container_name: nextcloud_app
    restart: always
    ports:
      - 8080:80
    depends_on:
      - db
    volumes:
      - ./app:/var/www/html
    environment:
      MYSQL_PASSWORD: "SENHA_APP"
      MYSQL_DATABASE: nextcloud
      MYSQL_USER: nextcloud
      MYSQL_HOST: db
```
exemplo de senha 
```
MYSQL_ROOT_PASSWORD: "root123forte"
MYSQL_PASSWORD: "nextcloud123forte"
```
---

## 3. O que cada parte faz

### Banco (db)


---

## 4. Gerar senha

```bash
openssl rand -base64 16 ( abra um novo terminal e gere as senhas aleatorias para uso )
```

Usa isso nas senhas do compose.

---

## 5. Subir o ambiente

```bash
docker compose up -d
```

O que faz:

* baixa imagens
* cria containers
* roda tudo em background

---

## 6. Acesso

* local: `http://localhost:8080`
* rede: `http://IP_DO_SERVIDOR:8080`

Na primeira tela:

* cria usuário admin
* conecta no banco

---

## 7. Problemas comuns

### Senha com caracteres especiais

Se quebrar:

* coloca entre aspas
* evita `$` em excesso

---


### Permissão de pasta

```bash
sudo chown -R 33:33 ~/docker/nextcloud/app
```

---

## 8. Debug

Ver containers:

```bash
docker ps
```

Logs:

```bash
docker logs nextcloud_app
```

---

## 9. Atualizar

```bash
docker compose pull
docker compose up -d
```
