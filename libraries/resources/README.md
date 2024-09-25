# RESOURCES LIBRARY

## DEFAULT PARAMETERS
```yaml
PROJECT_NAME: whoami

IMAGE:
  NAME: whoami
  SOURCE: traefik/whoami
  VER: "latest"
```

## OPTIONAL PARAMETERS
### Command
```yaml
COMMAND: 
  - sh
  - -c
  - "apt-get update && apt-get install -y cron && echo '*/5 * * * * www-data /usr/local/bin/php -f /var/www/html/cron.php' > /etc/cron.d/nextcloud-cron &&  chmod 0644 /etc/cron.d/nextcloud-cron &&  crontab /etc/cron.d/nextcloud-cron && service cron start && exec /entrypoint.sh apache2-foreground"
```
### Environments
```yaml
ENVS:
  - {name: TZ,                          type: text,   value: Europe/Moscow}
  - {name: EMAIL_DB_USER,               type: secret, key: prod}
```

### Service & Ingress[may be offed] (traefik)
```yaml
# DIRECT_CONNECTION: true
PORTS:
  # Create port entity in deploy/svc
  # - {name: http, port: 80,  protocol: TCP}
  # Create port entity in deploy/svc ingress
  - {name: http, port: 80,  protocol: TCP, ingress: true, entryPoint: websecure, host: test.example.com}

# MIDDLEWARES:
#   - nextcloud-redirectregex1
#   - nextcloud-redirectregex1
```

### VolumeMounts
```yaml
VOLUME_MOUNTS:
  # Secret
  - {name: secret, mountPath: "/etc/traefik/traefik.yml", subPath: traefik.yml,   readOnly: true, secretName: traefik.yml-secret,   key: prod}
  # PVC AWS_EFS
  - {name: ui-db,  mountPath: "/etc/x-ui", readOnly: false, storage: 5Gi, aws_efs_id: fs-0d3d63ffca2e02aad, aws_efs_ap: fsap-064778ed194093333}
  # LocalPath
  - {name: ui-db,  mountPath: "/etc/x-ui", readOnly: false, hostPath: /mnt/test}
```

### HealthCheck
```yaml
HEALTHCHECK:
  TYPE: tcp
  port: 6379
  initialDelaySeconds: 60
  periodSeconds: 30
  timeoutSeconds: 10
  failureThreshold: 3
# == OR ==
# HEALTHCHECK:
#   TYPE: exec
#   command: "curl -f http://localhost:80/ || exit 1"
#   initialDelaySeconds: 60
#   periodSeconds: 30
#   timeoutSeconds: 10
#   failureThreshold: 3
```

### Limits
```yaml
LIMIT: 
  CPU: "1"
  MEMORY: "512Mi"
```

### StorageClass (aws-efs)
```yaml
STORAGECLASSES:
  - {name: serviceName, aws_efs_id: fs-s7f8sf98s, state: present}
```

### Priority
```yaml
PRIORITY:
  VALUE: 1000
  PREFER_INSTANCE: 
    - medium
  STRICT: false
```