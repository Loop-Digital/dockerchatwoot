<p align="center">
<img src="https://cwmkt.com.br/wp-content/uploads/2024/04/logo_github.png" width="240" />
<p align="center">Seja bem-vindo ao Guia de Instalação Docker Chatwoot 🚀</p>
</p>
  
<p align="center"> 
<a href="https://hubconnect.top" target="_blank">👉 Participe da Comunidade HubConnect 👈</a>
</p>

<hr />
<hr />

### Caso não tenha Portainer e Traefix instalado, siga primeira etapa

<details>
<summary>Instalando Portainer e Traefix</summary>

### Atualizando Dependências

Atualize os repositórios do Ubuntu executando o seguinte comando:

```bash
sudo apt update && apt upgrade -y
```

----------------------------------------------------------------------------

**Instale o Docker em sua VPS**

```bash
sudo apt install docker.io -y
```

----------------------------------------------------------------------------

**Instalando Portainer**

```bash
docker swarm init
```

```bash
nano traefik.yml
```

```bash
version: "3.8"

services:

  traefik:
    image: traefik:2.11.1
    command:
      - "--api.dashboard=true"
      - "--providers.docker.swarmMode=true"
      - "--providers.docker.endpoint=unix:///var/run/docker.sock"
      - "--providers.docker.exposedbydefault=false"
      - "--providers.docker.network=ecosystem_network"
      - "--entrypoints.web.address=:80"
      - "--entrypoints.web.http.redirections.entryPoint.to=websecure"
      - "--entrypoints.web.http.redirections.entryPoint.scheme=https"
      - "--entrypoints.web.http.redirections.entrypoint.permanent=true"
      - "--entrypoints.websecure.address=:443"
      - "--certificatesresolvers.letsencryptresolver.acme.httpchallenge=true"
      - "--certificatesresolvers.letsencryptresolver.acme.httpchallenge.entrypoint=web"
      - "--certificatesresolvers.letsencryptresolver.acme.email=contato@seudominio.com.br"
      - "--certificatesresolvers.letsencryptresolver.acme.storage=/etc/traefik/letsencrypt/acme.json"
      - "--log.level=DEBUG"
      - "--log.format=common"
      - "--log.filePath=/var/log/traefik/traefik.log"
      - "--accesslog=true"
      - "--accesslog.filepath=/var/log/traefik/access-log"
    deploy:
      placement:
        constraints:
          - node.role == manager
      labels:
        - "traefik.enable=true"
        - "traefik.http.middlewares.redirect-https.redirectscheme.scheme=https"
        - "traefik.http.middlewares.redirect-https.redirectscheme.permanent=true"
        - "traefik.http.routers.http-catchall.rule=hostregexp(`{host:.+}`)"
        - "traefik.http.routers.http-catchall.entrypoints=web"
        - "traefik.http.routers.http-catchall.middlewares=redirect-https@docker"
        - "traefik.http.routers.http-catchall.priority=1"
    volumes:
      - "/var/run/docker.sock:/var/run/docker.sock:ro"
      - "traefik_certificates_volume:/etc/traefik/letsencrypt"
    ports:
      - target: 80
        published: 80
        mode: host
      - target: 443
        published: 443
        mode: host
    networks:
      - ecosystem_network

volumes:
  traefik_certificates_volume:
    external: true
    name: traefik_certificates_volume

networks:
  ecosystem_network:
    external: true
    name: ecosystem_network
 ```

```bash
nano portainer.yml
```

```bash
version: "3.8"

services:

  agent:
    image: portainer/agent:latest
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - /var/lib/docker/volumes:/var/lib/docker/volumes
    networks:
      - ecosystem_network
    deploy:
      mode: global
      placement:
        constraints: [node.platform.os == linux]

  portainer:
    image: portainer/portainer-ce:latest
    command: -H tcp://tasks.agent:9001 --tlsskipverify
    volumes:
      - portainer_volume:/data
    networks:
      - ecosystem_network
    deploy:
      mode: replicated
      replicas: 1
      placement:
        constraints: [node.role == manager]
      labels:
        - "traefik.enable=true"
        - "traefik.docker.network=ecosystem_network"
        - "traefik.http.routers.portainer.rule=Host(`seudominio.com.br`)"
        - "traefik.http.routers.portainer.entrypoints=websecure"
        - "traefik.http.routers.portainer.priority=1"
        - "traefik.http.routers.portainer.tls.certresolver=letsencryptresolver"
        - "traefik.http.routers.portainer.service=portainer"
        - "traefik.http.services.portainer.loadbalancer.server.port=9000"

networks:
  ecosystem_network:
    external: true
    attachable: true
    name: ecosystem_network

volumes:
  portainer_volume:
    external: true
    name: portainer_volume

 ```

```bash
docker swarm init
```

docker swarm init
```bash
docker network create --driver=overlay ecosystem_network
```

```bash
docker stack deploy --prune --resolve-image always -c traefik.yml traefik
```

```bash
docker stack deploy --prune --resolve-image always -c portainer.yml portainer
```

Acesse URL de seu Site e Crie Usuario


</details>


Postgres

### Adicione Stack abaixo, Stack > add stack

![image](https://github.com/cwmkt/dockerquepasa/assets/91642837/623a6dc6-c231-4105-9a02-3070d894adb8)

```bash
version: "3.8"

services:

  postgresql:
    image: bitnami/postgresql:15
    restart: always
    environment:
      POSTGRESQL_USERNAME: postgres
      POSTGRESQL_PASSWORD: r45796yv3bhub9w4f3ga3ikxmxos648r
    networks:
      - ecosystem_network
    ports:
      - 5432:5432
    volumes:
      - postgresql_volume:/bitnami/postgresql
      - postgresql_config_volume:/opt/bitnami/postgresql/conf
    deploy:
    

volumes:
  postgresql_volume:
    external: true
    name: postgresql_volume
  postgresql_config_volume:
    external: true
    name: postgresql_config_volume
    
networks:
  ecosystem_network:
    external: true
    name: ecosystem_network
```

Depois clique em DEPLOY

![image](https://github.com/cwmkt/dockerquepasa/assets/91642837/bdc62781-993a-4d31-b8cd-5cd6466900f5)

Acesse portainer, vá até contaneir Postgres, crie banco de dados

![image](https://github.com/cwmkt/woofedcrm/assets/91642837/503bf33f-ff42-4fe5-9a8f-a98e2d80d6e4)

![image](https://github.com/cwmkt/woofedcrm/assets/91642837/67eb98c2-f7e7-4f27-ae9d-1befc672edcf)


```bash
psql -U postgres
```

```bash
create database chatwoot;
```

Redis
Para instalar o redis vamos fazer as mesmas etapas que fizemos no postgres: Vamos para aba Stacks e depois em add Stack

![image](https://github.com/cwmkt/woofedcrm/assets/91642837/07c67cb5-465c-4e05-88e7-164ec8456f00)

Colocamos o nome da stack (recomendo deixar redis)

![image](https://github.com/cwmkt/woofedcrm/assets/91642837/03d15854-59bc-47df-a983-ad9adf0c93c4)

Cole a stack do redis abaixo no seu portainer:

```bash
version: '3.7'

services:
  redis:
    image: redis:latest
    command: [
        "redis-server",
        "--appendonly",
        "yes",
        "--port",
        "6379"
      ]
    volumes:
      - redis_data:/data
    networks:
      - ecosystem_network
    deploy:
      placement:
        constraints:
          - node.role == manager

volumes:
  redis_data:
    external: true
    name: redis_woofedcrm_data

networks:
  ecosystem_network
    external: true
    ecosystem_network
```

![image](https://github.com/cwmkt/woofedcrm/assets/91642837/dedb5386-bc42-465c-a39e-1ff2aa131f85)

e pronto, o redis esta instalado e funcionando na sua VPS


### Adicione Stack abaixo, Stack > add stack

![image](https://github.com/cwmkt/dockerquepasa/assets/91642837/623a6dc6-c231-4105-9a02-3070d894adb8)

Lembre de Alterar os dados 

```bash
version: "3.7"

services:
  chatwoot:
    image: chatwoot/chatwoot:latest
    command: bundle exec rails s -p 3000 -b 0.0.0.0
    entrypoint: docker/entrypoints/rails.sh
    volumes:
      - chatwoot_assets:/app/public
    networks:
      - ecosystem_network
    environment:
      - INSTALLATION_NAME=chat
      - NODE_ENV=production
      - RAILS_ENV=production
      - INSTALLATION_ENV=docker
      - SECRET_KEY_BASE=a7094a88032d106067b60b72e5aed6af
      - FRONTEND_URL=https://seudominio.com.br
      - DEFAULT_LOCALE=pt_BR
      - FORCE_SSL=true
      - ENABLE_ACCOUNT_SIGNUP=false
      - REDIS_URL=redis://redis:6379
      - POSTGRES_HOST=postgres
      - POSTGRES_USERNAME=postgres
      - POSTGRES_PASSWORD=Senha Postgres
      - POSTGRES_DATABASE=chatwoot
      - ACTIVE_STORAGE_SERVICE=local
      - RAILS_LOG_TO_STDOUT=true
      - USE_INBOX_AVATAR_FOR_BOT=true
      - MAILER_SENDER_EMAIL=contato@seudominio.com.br <contato@seudominio.com.br>
      - SMTP_DOMAIN=seudominio.com.br
      - SMTP_ADDRESS=mail.seudominio.com.br
      - SMTP_PORT=587
      - SMTP_USERNAME=contato@seudominio.com.br
      - SMTP_PASSWORD=Senha SMTP
      - SMTP_AUTHENTICATION=login
      - SMTP_ENABLE_STARTTLS_AUTO=true
      - SMTP_OPENSSL_VERIFY_MODE=peer
      - MAILER_INBOUND_EMAIL_DOMAIN=seudominio.com.br
      - DISABLE_TELEMETRY=true
    deploy:
      mode: replicated
      replicas: 1
      placement:
        constraints:
          - node.role == manager
      resources:
        limits:
          cpus: "1"
          memory: 1024M
      labels:
        - traefik.enable=true
        - traefik.http.routers.chatwoot.rule=Host(`app.seudominio.com.br`)
        - traefik.http.routers.chatwoot.entrypoints=websecure
        - traefik.http.routers.chatwoot.tls.certresolver=letsencryptresolver
        - traefik.http.routers.chatwoot.priority=1
        - traefik.http.routers.chatwoot.service=chatwoot
        - traefik.http.services.chatwoot.loadbalancer.server.port=3000 
        - traefik.http.services.chatwoot.loadbalancer.passhostheader=true 
        - traefik.http.middlewares.sslheader.headers.customrequestheaders.X-Forwarded-Proto=https
        - traefik.http.routers.chatwoot.middlewares=sslheader@docker


  chatwoot_sidekiq:
    image: chatwoot/chatwoot:latest
    command: bundle exec sidekiq -C config/sidekiq.yml
    volumes:
      - chatwoot_assets:/app/public

    networks:
      - ecosystem_network
    environment:
      - INSTALLATION_NAME=chat
      - NODE_ENV=production
      - RAILS_ENV=production
      - INSTALLATION_ENV=docker
      - DISABLE_TELEMETRY=true
      - SECRET_KEY_BASE=a7094a88032d106067b60b72e5aed6af
      - FRONTEND_URL=https://seudominio.com.br
      - DEFAULT_LOCALE=pt_BR
      - FORCE_SSL=true
      - ENABLE_ACCOUNT_SIGNUP=false
      - REDIS_URL=redis://redis:6379
      - POSTGRES_HOST=postgres
      - POSTGRES_USERNAME=postgres
      - POSTGRES_PASSWORD=Senha Postgres
      - POSTGRES_DATABASE=chatwoot
      - ACTIVE_STORAGE_SERVICE=local
      - STORAGE_FORCE_PATH_STYLE:true
      - RAILS_LOG_TO_STDOUT=true
      - USE_INBOX_AVATAR_FOR_BOT=true
      - MAILER_SENDER_EMAIL=contato@seudominio.com.br <contato@seudominio.com.br>
      - SMTP_DOMAIN=seudominio.com.br
      - SMTP_ADDRESS=mail.seudominio.com.br
      - SMTP_PORT=465
      - SMTP_USERNAME=contato@seudominio.com.br
      - SMTP_PASSWORD= Senha SMTP
      - SMTP_AUTHENTICATION=login
      - SMTP_ENABLE_STARTTLS_AUTO=true
      - SMTP_OPENSSL_VERIFY_MODE=peer
      - MAILER_INBOUND_EMAIL_DOMAIN=contato@seudominio.com.br
    deploy:
      mode: replicated
      replicas: 1
      placement:
        constraints:
          - node.role == manager
      resources:
        limits:
          cpus: "1"
          memory: 1024M
volumes:
  chatwoot_assets:
    external: true
    name: chatwoot_assets

networks:
  ecosystem_network:
    external: true
    name: ecosystem_network
```

Depois clique em DEPLOY

![image](https://github.com/cwmkt/dockerquepasa/assets/91642837/bdc62781-993a-4d31-b8cd-5cd6466900f5)


Acesse: seudominio.com.<br>

Faça seu cadastro

**Pronto tudo Funcionando** ✅😎
