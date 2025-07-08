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
> no action required

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
RELEASE_NAMESPACE=example
CHART_VERSION=21.11.11

# Set the name of the domain where ezdrp will exist
APP_DOMAIN=example.domain.name
# Set the TLS certificate filename which will be pulled into tls secret
CERTIFICATE_NAME=$APP_DOMAIN
# Path to direcrory where TLS key and certs are stored
CERTS_PATH=~/certs
# Set environmental variable for storage class - to get available run: "kubectl get storageclass"
K8S_SC=longhorn
# Random it by default or set own password
APP_USER_PASSWD=$(openssl rand -hex 10)

# Username for postgres from EZD RP Backend
PSQL_USER=postgres

# ezd-pass.sh file should exists from ezd-backend installation
source /tmp/ezd-pass.sh

# Default values
POSTGRES_HOST=lp-backend-postgresql-rw.${BACKEND_NAMESPACE}.svc
RABBITMQ_HOST=lp-backend-rabbitmq.${BACKEND_NAMESPACE}.svc
REDIS_HOST=lp-backend-redis.${BACKEND_NAMESPACE}.svc
REDIS_APPEND_HOST=lp-backend-redis-append.${BACKEND_NAMESPACE}.svc
RABBITMQ_PORT=5672
REDIS_PORT=6379
REDIS_APPEND_PORT=6379
```

### Go go helm

```bash
cat <<EOF > /tmp/values.yaml
storage:
  storageClass: ${K8S_SC}
domainInfo:
  name: ${APP_DOMAIN}
  cert_name: ezdrp-cert
  cert_info: true
network:
  ingressName: nginx
preparing:
  basicAuth:
    username: ezdrpuser
    password: ${APP_USER_PASSWD}
redisExt:
  password: ${REDIS_PASSWD}
  host: ${REDIS_HOST}
  port: ${REDIS_PORT}
rabbitExt:
  user: ${RABBITMQ_USER}
  password: ${RABBITMQ_PASSWD}
  host: ${RABBITMQ_HOST}
  port: ${RABBITMQ_PORT}
redisAppendExt:
  password: ${REDIS_PASSWD}
  host: ${REDIS_APPEND_HOST}
  port: ${REDIS_APPEND_PORT}
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

# Make a secret containing TLS certificate chain
cat ~/certs/$CERTIFICATE_NAME.crt ~/certs/ca.crt > ~/certs/chain.crt

kubectl -n ${RELEASE_NAMESPACE} create secret tls ezdrp-cert --cert=$CERTS_PATH/chain.crt --key=$CERTS_PATH/$CERTIFICATE_NAME.key

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

## GUI Installation
If You want to install ezd-frontend via GUI, please follow [this instruction](https://github.com/linuxpolska/ezd-rp/blob/main/INSTALL_VIA_GUI.md).
