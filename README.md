# HELM-CHARTS

## Usage
### Pre-actions
```bash
helm dep build <path/to/chart/dir>
```
OR use ```--dependency-update``` key
```bash
helm install <release name> <path/to/chart/dir> -f <path/to/values.yaml> --dependency-update
```

### Create values 
See chart list of depends and use ```example.yaml``` in libraries

### Depends
#### Traefik
- libraries/resources/limits
- libraries/resources/ports
- libraries/resources/healthcheck
- libraries/resources/volumeMounts
- libraries/resources/volumes
- libraries/resources/svc

#### Universal
- libraries/resources/securityContext
- libraries/resources/priority
- libraries/resources/command
- libraries/resources/args
- libraries/resources/envs
- libraries/resources/limits
- libraries/resources/ports
- libraries/resources/healthcheck
- libraries/resources/volumeMounts
- libraries/resources/volumes
- libraries/resources/ingress
- libraries/resources/priorityClass
- libraries/resources/pvc
- libraries/resources/storageClass
- libraries/resources/svc