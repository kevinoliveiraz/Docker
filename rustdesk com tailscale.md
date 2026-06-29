
## 📦 Pré-requisitos

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

## 📁 1. Criar estrutura do projeto

```bash
mkdir -p ~/rustdesk-server
cd ~/rustdesk-server
```

---

## 🧾 2. Criar docker-compose.yml

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

## 💾 Salvar yml

Ctrl +O , Enter, Ctrl X

---

## 🌐 3. Descobrir seu IP do Tailscale

```bash
tailscale ip -4
```

Exemplo de saída:

```text
100.101.102.103
```

👉 Substitua no docker-compose.yml:

```bash
command: hbbs -r 100.101.102.103:21117
```

---

## ▶️ 4. Subir o servidor

```bash
docker compose up -d
```

Verificar se está rodando:

```bash
docker ps
```

---

## 🔑 5. Obter chave pública

```bash
cat ~/rustdesk-server/data/id_ed25519.pub
```

Exemplo:

```text
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

👉 Guarde isso — será usado nos clientes

---

## 📱 6. Configurar cliente RustDesk

No app RustDesk (celular ou PC):

Vá em:

Settings → Network → ID/Relay Server

Preencha:

* ID Server: SEU_IP_TAILSCALE
* Relay Server: SEU_IP_TAILSCALE
* Key: (cole a chave pública)

---

## 🧪 7. Teste de conexão

1. Conecte dois dispositivos ao Tailscale
2. Abra RustDesk nos dois
3. Use o ID para conectar

---

## 🔥 8. Portas utilizadas (referência técnica)

| Porta | Função            |
| ----- | ----------------- |
| 21115 | NAT Test          |
| 21116 | Conexão principal |
| 21117 | Relay             |
| 21118 | Web/HTTP          |

👉 Com Tailscale: NÃO precisa abrir portas no roteador

---

## ⚙️ 9. Ver logs (debug)

```bash
docker logs rustdesk-hbbs
docker logs rustdesk-hbbr
```

---

## 🔄 10. Reiniciar serviços

```bash
docker compose restart
```

Parar:

```bash
docker compose down
```

---

## 💾 11. Persistência de dados

Todos os dados ficam em:

```text
~/rustdesk-server/data
```

👉 Isso inclui:

* chave criptográfica
* configurações

---



