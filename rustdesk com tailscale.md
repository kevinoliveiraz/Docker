
# Pré-requisitos

Antes de começar, você precisa ter:

### ✔ Docker instalado
```bash
docker --version
````

### ✔ Docker Compose

```bash
docker compose version
```

### ✔ Tailscale funcionando

```bash
tailscale status
```

Se não estiver logado:

```bash
tailscale up
```

---

## 1. Criar estrutura do projeto

```bash
mkdir -p ~/rustdesk-server
cd ~/rustdesk-server
```

---

##  2. Criar docker-compose.yml

Crie o arquivo:

```bash
nano docker-compose.yml
```

Cole:

```yaml
version: '3'

services:
  hbbs:
    image: rustdesk/rustdesk-server:latest
    container_name: rustdesk-hbbs
    command: hbbs -r SEU_IP_TAILSCALE:21117
    network_mode: host
    volumes:
      - ./data:/root
    restart: unless-stopped

  hbbr:
    image: rustdesk/rustdesk-server:latest
    container_name: rustdesk-hbbr
    command: hbbr
    network_mode: host
    volumes:
      - ./data:/root
    restart: unless-stopped
```

---

## Salvar yml

Ctrl +O , Enter, Ctrl X

---

##  3. Descobrir seu IP do Tailscale

```bash
tailscale ip -4
```

Exemplo de saída:

```text
100.101.102.103
```

 Substitua no docker-compose.yml:

```bash
command: hbbs -r 100.101.102.103:21117
```

---

##  4. Subir o servidor

```bash
docker compose up -d
```

Verificar se está rodando:

```bash
docker ps
```

---

##  5. Obter chave pública

```bash
cat ~/rustdesk-server/data/id_ed25519.pub
```

Exemplo:

```text
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

Guarde isso — será usado nos clientes

---

##  6. Portas utilizadas (referência técnica)

| Porta | Função            |
| ----- | ----------------- |
| 21115 | NAT Test          |
| 21116 | Conexão principal |
| 21117 | Relay             |
| 21118 | Web/HTTP          |

Com Tailscale: NÃO precisa abrir portas no roteador

---

## 7. Persistência de dados

Todos os dados ficam em:

```text
~/rustdesk-server/data
```
👉 Isso inclui:

* chave criptográfica
* configurações



---
#  Configurando o Cliente RustDesk

Após iniciar o servidor RustDesk, é necessário configurar cada dispositivo cliente para utilizar seu servidor privado.

---

## 1. Acesse as configurações de rede

No RustDesk, clique nos 3 pontos

<img width="1717" height="916" alt="1" src="https://github.com/user-attachments/assets/4be760a9-7de7-403a-9833-cce22f20b0e9" />

**Configurações **

Em seguida, selecione rede e clique em **Servidor ID/Relay**.

> <img width="1717" height="916" alt="2" src="https://github.com/user-attachments/assets/25f9a392-2eec-4752-8dd0-282a26eef0b5" />


---

## 2. Configure o servidor

Na janela **Servidor ID/Relay**, preencha os campos da seguinte forma:

| Campo | Valor |
|--------|-------|
| **Servidor de ID** | `SEU_IP_TAILSCALE:21116` |
| **Servidor de Relay** | `SEU_IP_TAILSCALE:21117` |
| **Servidor da API** | Deixe em branco |
| **Key** | Cole a chave pública (`id_ed25519.pub`) |

Depois clique em **OK**.

<img width="1717" height="916" alt="3" src="https://github.com/user-attachments/assets/b1f452e6-52c0-41ca-b4b9-57419ed65a7b" />


---



## 4. Verifique se está funcionando

Após salvar as configurações, volte para a tela inicial do RustDesk.

Confira se:

- O status aparece como **Pronto**.
- Seu computador recebeu um **ID**.

Esse ID será utilizado para que outros dispositivos possam acessar este computador.



## 5. Testando a conexão

Em outro computador conectado ao Tailscale:

1. Abra o RustDesk.
2. faça a mesma configuração em Servidor Id/Relay
3. Digite o ID do computador remoto.
4. Clique em **Conectar**.
5. Informe a senha ou aceite a solicitação de conexão.

Se tudo estiver configurado corretamente, a conexão será realizada utilizando seu servidor RustDesk privado.



