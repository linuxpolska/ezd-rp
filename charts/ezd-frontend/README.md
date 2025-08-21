## General

### Are you looking for more information?

1. Documentation: https://github.com/linuxpolska/ezd-rp/blob/main/README.md
2. Chart Source: https://git.eadministracja.nask.pl/api/packages/ezdrp/helm


## Before Installation

> **Note:**
>
> Use notes from ezd-backend helm installation output.
>
> OR
>
> Use file `/tmp/ezd-pass.sh` created in ezd-backend helm installation for CLI.

#### Prerequisites

- Kubernetes 1.19+
- Helm 3.2.0+
- PV provisioner support in the underlying infrastructure

## After Installation

> **Note:**
> - Change Kuip root password
> `kubectl -n $RELEASE_NAMESPACE set env deploy kuip-api KUIP_ROOT_RESET_PASSWORD=change-me`
>
> - If you use selfsigned TLS certyficate, you will need its CA as trusted in your browser. To get it:
> `kubectl get secrets -n ezd-frontend ezd-frontend-selfsigned-ca -o go-template='{{ index .data "ca.crt" }}' | base64 -d > ca.crt`
> Note that `ezd-frontend-selfsigned-ca` is in format `${HELM_RELEASE}-selfsigned-ca`


## Before Upgrade

> **Note:**
> no action required

## After Upgrade

> **Note:**
> no action required


## Tips and Tricks

> **Note:**
> List all releases using `helm list`

## Known Issues

> **Note:**
> Notify us: https://github.com/linuxpolska/ezd-rp/issues

## CLI installation

### Preparation

> **Note:**
> Installation of the EZD RP frontend requires a TLS certificate. Example creation is described [here](README_TLS.md)

```bash
RELEASE_NAMESPACE=ezd-frontend
CHART_VERSION=2.0.0

# Set the name of the domain where ezdrp will exist
APP_DOMAIN=example.domain.name
K8S_SC=longhorn
# Random it by default or set own password
APP_USER_PASSWD=$(openssl rand -hex 10)

# Username for postgres from EZD RP Backend
PSQL_USER=postgres

# ezd-pass.sh file should exists from ezd-backend installation
source /tmp/ezd-pass.sh

# Default values
POSTGRES_HOST=postgresql-rw.${BACKEND_NAMESPACE}.svc
RABBITMQ_HOST=rabbitmq.${BACKEND_NAMESPACE}.svc
REDIS_HOST=redis.${BACKEND_NAMESPACE}.svc
REDIS_APPEND_HOST=redis-append.${BACKEND_NAMESPACE}.svc
```

### Go go helm

```bash
cat <<EOF > /tmp/values.yaml
certManager:
  enabled: true # enable cert-manager to manage TLS certs for ezd-frontend
  letsencrypt:
    enabled: false # enable letsencrypt CA
    email: jon@example.com
  organizations:
    - example-org

####
frontend:
  storage:
    storageClass: ${K8S_SC}
  domainInfo:
    name: ${APP_DOMAIN}
  network:
    ingressName: nginx
  preparing:
    basicAuth:
      username: ezdrpuser
      password: ${APP_USER_PASSWD}
  redisExt:
    password: ${REDIS_PASSWD}
    host: ${REDIS_HOST}
  rabbitExt:
    user: ${RABBITMQ_USER}
    password: ${RABBITMQ_PASSWD}
    host: ${RABBITMQ_HOST}
  redisAppendExt:
    password: ${REDIS_PASSWD}
    host: ${REDIS_APPEND_HOST}
  email:
    host: localhost
    port: 587
    sendermail: test@mail.loc
    sendername: test@mail.loc
    password: qwerty123
    from: "EZD RP"
  ezdrpApi:
    persistence:
      storageClass: ${K8S_SC}
  filerepository:
    persistence:
      storageClass: ${K8S_SC}
      size: 500 # Gi
  ssoIdentityServer:
    persistence:
      storageClass: ${K8S_SC}
  wpeRest:
    persistence:
      storageClass: ${K8S_SC}
  cloudadmin:
    relationaldb:
      connectiontype:
        ezdrp: POSTGRESQL
        archiwum: POSTGRESQL
        kuip: POSTGRESQL
        ezdrpodczyt: POSTGRESQL
        wpa: POSTGRESQL
        teryt: POSTGRESQL
      connectionstring:
        ezdrp: Host=${POSTGRES_HOST};Port=5432;Database=ezdrp;Username=${PSQL_USER};Password=${PSQL_PASSWD}
        archiwum: Host=${POSTGRES_HOST};Port=5432;Database=archiwum;Username=${PSQL_USER};Password=${PSQL_PASSWD}
        kuip: Host=${POSTGRES_HOST};Port=5432;Database=ezdrp;Username=${PSQL_USER};Password=${PSQL_PASSWD}
        ezdrpodczyt: Host=${POSTGRES_HOST};Port=5432;Database=ezdrp_odczyt;Username=${PSQL_USER};Password=${PSQL_PASSWD}
        wpe: Host=${POSTGRES_HOST};Port=5432;Database=ezdrp_odczyt;Username=${PSQL_USER};Password=${PSQL_PASSWD}
        teryt: Host=${POSTGRES_HOST};Port=5432;Database=ezdrp;Username=${PSQL_USER};Password=${PSQL_PASSWD}
EOF

helm -n ${RELEASE_NAMESPACE} upgrade --install ezd-frontend-release \
--repo https://git.eadministracja.nask.pl/api/packages/ezdrp/helm \
nask-ezdrp-ha \
-f /tmp/values.yaml \
--version ${CHART_VERSION} \
--create-namespace
```

### Validation and Testing

```bash
kubectl -n ${RELEASE_NAMESPACE} get po
helm -n ${RELEASE_NAMESPACE} list
```

## CLI removing

```bash
helm -n ${RELEASE_NAMESPACE} uninstall ezd-frontend-release
```
