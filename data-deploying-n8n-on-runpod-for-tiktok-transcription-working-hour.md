# [Data] - Deploying n8n on RunPod for TikTok transcription (Working Hour)

## Deploy n8n on RunPod:

### 1. Image
```
n8nio/n8n:latest
```

### 2. Environment variables
```
N8N_PORT=5678
N8N_HOST=localhost
N8N_PROTOCOL=http
GENERIC_TIMEZONE=UTC+7
DB_TYPE=sqlite
DB_SQLITE_DATABASE=/workspace/node/.n8n/data/database.sqlite
N8N_DIAGNOSTICS_ENABLED=false
N8N_ENCRYPTION_KEY=lrjil0cMuv0TnGmG2qWCGJseUAAuhFc8Vug02aZ0kXs=
N8N_RUNNERS_ENABLED=true
N8N_ENFORCE_SETTINGS_FILE_PERMISSIONS=true
N8N_SECURE_COOKIE=false
N8N_BASIC_AUTH_ACTIVE=true
N8N_PERSONALIZATION_ENABLED=false
N8N_VERSION_NOTIFICATIONS_ENABLED=false
N8N_EXTERNAL_STORAGE_DISABLE_TELEMETRY=true
N8N_TEMPLATES_ENABLED=false
```

### 3. Expose port
```
5678
```

## Note
Just stop the pod, the volume will store in the workspace (it charges only for volume), so when start again, it will reuse our old data. If we terminate the pod, all data will delete all.
