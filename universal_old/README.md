# UNIVERSAL CHART

## Example ```values.yaml```
```yaml

DAEMONSET: false #may be "true"

PROJECT_NAME: whoami

IMAGE:
  NAME: whoami
  SOURCE: traefik/whoami
  VER: "latest"

# COMMAND: 
#   - sh
#   - -c
#   - "apt-get update && apt-get install -y cron && echo '*/5 * * * * www-data /usr/local/bin/php -f /var/www/html/cron.php' > /etc/cron.d/nextcloud-cron &&  chmod 0644 /etc/cron.d/nextcloud-cron &&  crontab /etc/cron.d/nextcloud-cron && service cron start && exec /entrypoint.sh apache2-foreground"


# ENVS:
#   - {name: TZ,                          type: text,   value: Europe/Moscow}
#   - {name: EMAIL_DB_USER,               type: secret, key: prod}

# HOSTPORT: false
# DIRECT_CONNECTION: false
PORTS:
  # Create port entity in deploy/svc
  # - {name: http, port: 80,  protocol: TCP}
  # Create port entity in deploy/svc ingress
  - {name: http, port: 80,  protocol: TCP}
  
VOLUME_MOUNTS:
  # Secret
  - {name: secret, mountPath: "/etc/traefik/traefik.yml", subPath: traefik.yml,   readOnly: true, secretName: traefik.yml-secret,   key: prod}
  # PVC AWS_EFS
  - {name: ui-db,  mountPath: "/etc/x-ui", readOnly: false, storage: 5Gi, aws_efs_id: fs-0d3d63ffca2e02aad, aws_efs_ap: fsap-064778ed194093333}
  # LocalPath
  - {name: ui-db,  mountPath: "/etc/x-ui", readOnly: false, hostPath: /mnt/test}
# MIDDLEWARES:
#   - nextcloud-redirectregex1
#   - nextcloud-redirectregex1

# HEALTHCHECK:
#   TYPE: tcp
#   port: 6379
#   initialDelaySeconds: 60
#   periodSeconds: 30
#   timeoutSeconds: 10
#   failureThreshold: 3
# == OR ==
# HEALTHCHECK:
#   TYPE: exec
#   command: "curl -f http://localhost:80/ || exit 1"
#   initialDelaySeconds: 60
#   periodSeconds: 30
#   timeoutSeconds: 10
#   failureThreshold: 3

# CONFIGMAPS:
#   TRAEFIK:
#     SSL:
#       - {cert: "/run/secrets/example.com.crt", key: "/run/secrets/example.com.key"}

# CONFIGMAPS:
#   PROMETHEUS:
#     HOSTS:
#       - {jobName: prometheus, targets: [{url: localhost:9090,   labels: prometheus}]}
#       - {jobName: traefik,    targets: [{url: traefik-svc:8082, labels: traefik   }]}

#       - {jobName: masters, targets: [
#           {url: "[2a05<...>1bb2]:9100", labels: master-0},
#           {url: "[2a05:<...>468]:9100", labels: master-1},
#         ]}
#       - {jobName: workers, targets: [
#           {url: "[2a05:d0<...>:5a9f]:9100",   labels: worker-0},
#           {url: "[2a05:d<...>1:f3ca]:9100",   labels: worker-1},
#           {url: "[2a03:b0<...>87d:e000]:9100",labels: worker-2},
#           {url: "[2a00:<...>005:1::350]:9100",labels: worker-3},
#         ]}

# LIMIT: 
#   CPU: "1"
#   MEMORY: "512Mi"

# STORAGECLASSES:
#   - {name: serviceName, aws_efs_id: fs-s7f8sf98s, state: present}

# PRIORITY:
#   VALUE: 1000
#   NODE_SELECTOR: [
#     {label: cloud, value: aws  },
#     {label: key,   value: value},
#   ]
#   STRICT: false
```