# Local - n8n Docker

## Prepare for n8n deployment:

1. Setup Docker compose for deploy n8n (use official image from Docker Hub).
2. Tested shutdown and restart the container to ensure it nothing problem.

## Docker Compose Configuration

```yaml
services:
  n8n:
    image: n8nio/n8n:stable
    restart: always
    ports:
      - "5678:5678"
    environment:
      - N8N_PORT=5678
      - N8N_HOST=localhost
      - N8N_PROTOCOL=http
      - GENERIC_TIMEZONE=${GENERIC_TIMEZONE}
      - DB_TYPE=sqlite
      - DB_SQLITE_DATABASE=/data/database.sqlite
      - N8N_DIAGNOSTICS_ENABLED=false
      - N8N_ENCRYPTION_KEY=${ENCRYPTION_KEY}
      - N8N_RUNNERS_ENABLED=true
      - N8N_ENFORCE_SETTINGS_FILE_PERMISSIONS=true
      - N8N_SECURE_COOKIE=false
      - N8N_BASIC_AUTH_ACTIVE=true
      - N8N_PERSONALIZATION_ENABLED=false
      - N8N_VERSION_NOTIFICATIONS_ENABLED=false
      - N8N_EXTERNAL_STORAGE_DISABLE_TELEMETRY=true
      - N8N_TEMPLATES_ENABLED=false

    volumes:
      - n8n_data:/home/node/.n8n
      - n8n_data:/data
    
    networks:
      - n8n_network

volumes:
  n8n_data:
    driver: local
    name: n8n_data
  
networks:
  n8n_network:
    driver: bridge
    name: n8n_network
```
