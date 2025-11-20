#🚀 6. COMO OS SERVIÇOS SE REGISTRAM NO TRAEFIK

Agora seus outros projetos terão labels, e o Traefik os enxerga via Docker.

Exemplo: frontend Vite
Arquivo docker-compose.yml do seu app → separado.

```yaml
version: "3.9"

services:
  frontend:
    image: node:18
    working_dir: /app
    command: npm run dev
    ports:
      - "5173:5173"
    volumes:
      - ./:/app
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.front.rule=Host(`meu-dominio.com`)"
      - "traefik.http.routers.front.entrypoints=websecure"
      - "traefik.http.routers.front.tls.certresolver=letsencrypt"
      - "traefik.http.services.front.loadbalancer.server.port=5173"
```

#API com proxy equivalente ao Vite (/api → backend:3000)
```yaml
  api:
    image: node:18
    working_dir: /app
    command: node server.js
    volumes:
      - ./api:/app
    labels:
      - "traefik.enable=true"

      # Match igual ao comportamento do Vite
      - "traefik.http.routers.api.rule=Host(`meu-dominio.com`) && PathPrefix(`/api`)"

      - "traefik.http.routers.api.entrypoints=websecure"
      - "traefik.http.routers.api.tls.certresolver=letsencrypt"

      - "traefik.http.routers.api.middlewares=strip-api"

      # Porta interna do container
      - "traefik.http.services.api.loadbalancer.server.port=3000"
```
