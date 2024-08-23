# INSTRUCTION FOR RECOVERY OF POSTGRESQL DB FOR EZD-BACKEND CHART

Table of Contents 
==========
[[_TOC_]]


# Prerequisites

## Preparation of resources
1. Variable export

```bash
export K8S_NAMESPACE=
export DB_NAME=
export STORAGE_CLASS=
export S3_BUCKET=
export S3_URL=
export CLUSTER_NAME=
export MINIO_CREDENTIALS=
export S3_CACERT=
export PSQL_PASSWD=
export PSQL_APP_PASSWD=
export RABBITMQ_PASSWD=
export RABBITMQ_USER=
export REDIS_PASSWD=
export PG_APP_USERNAME=
```

2. ezdrp-adm-secret

```bash
cat <<EOF > /tmp/ezdrp-adm-secret.yaml
apiVersion: v1
kind: Secret
metadata:
  name: adm-secret
  namespace: "${K8S_NAMESPACE}"
type: kubernetes.io/basic-auth
stringData:
  username: <username>
  password: <password>
EOF
```

- Create secret adm-secret

```bash
kubectl apply -f /tmp/ezdrp-adm-secret.yaml
```

3. ezdrp-app-secret

```bash
cat <<EOF > /tmp/ezdrp-app-secret.yaml
apiVersion: v1
kind: Secret
metadata:
  name: app-secret
  namespace: "${K8S_NAMESPACE}"
type: kubernetes.io/basic-auth
stringData:
  username: <username>
  password: <password>
EOF
```

- Create secret ezdrp-app-secret

```bash
kubectl apply -f /tmp/ezdrp-app-secret.yaml
```

4. minio-creds

```bash
kubectl -n "${K8S_NAMESPACE}" create secret generic minio-creds   --from-literal=ACCESS_SECRET_ID=<secret_id>   --from-literal=ACCESS_SECRET_KEY=<secret_key>
```

## Postgresql deploy together with barman in version 1.6.1.

- backup option is already added to version 1.6.1 of the charts you jus need to pass variables in instalation via CLI:

```bash
cat <<EOF > /tmp/values-backend.yaml

global:
  postgresql:
    deploy: true
  rabbitmq:
    deploy: true
  redis:
    deploy: true
postgresqlConfig:
  auth:
    admPassword: ${PSQL_PASSWD}
    appPassword: ${PSQL_PASSWD}
    bootstrap:
      initdb:
        database: "${DB_NAME}"
        owner: "${PG_APP_USERNAME}"
        secret:
          name: "false"
  customConfig:
    instances: 1
    minSyncReplicas: 0
    maxSyncReplicas: 0
    backup:
      enabled: false
      barmanObjectStore:
        destinationPath: "s3://${S3_BUCKET}/"
        endpointURL: "${S3_URL}"
        endpointCA:
          name: "${S3_CACERT}"
          key: "cacerts.pem"
        s3Credentials:
          accessKeyId:
            name: "${MINIO_CREDENTIALS}"
            key: ACCESS_SECRET_ID
          secretAccessKey:
            name: "${MINIO_CREDENTIALS}"
            key: ACCESS_SECRET_KEY
        wal:
          compression: gzip
          maxParallel: 1
        data:
          compression: gzip
          immediateCheckpoint: true
          jobs: 2
      retentionPolicy: "30d"
rabbitmqConfig:
  auth:
    password: ${RABBITMQ_PASSWD}
    username: ${RABBITMQ_USER}
redisConfig:
  auth:
    password: ${REDIS_PASSWD}

EOF
```

## Backup creation

```bash
cat <<EOF > /tmp/backup.yaml
apiVersion: postgresql.cnpg.io/v1
kind: ScheduledBackup
metadata:
  name: ${DB_NAME} #nazwa backupu
  namespace: ${K8S_NAMESPACE}
spec:
  schedule: "0 0 0 * * *"
  backupOwnerReference: self
  immediate: true
  cluster:
    name: ${CLUSTER_NAME}  #name of  cluster
```


## Recover of  postgresql cluster

```bash
cat <<EOF> /tmp/cluster-restore.yaml
---
apiVersion: postgresql.cnpg.io/v1
kind: Cluster
metadata:
  name: ${DB_NAME}
  namespace: ${K8S_NAMESPACE}
spec:
  instances: 1
  minSyncReplicas: 0
  maxSyncReplicas: 0
  replicationSlots:
    highAvailability:
      enabled: true
  imageName: quay.io/linuxpolska/ezd-backend_postgresql:16.3-postgres-16.3-bullseye-r1
  monitoring:
    enablePodMonitor: true
  postgresql:
     pg_hba:
     - host all all all password
     parameters:
       max_connections: "100"
       superuser_reserved_connections: "3"
       shared_buffers: "128MB"
  resources:
    requests:
      memory: "2Gi"
      cpu: "2"
    limits:
      memory: "2Gi"
      cpu: "2"
  superuserSecret:
    name: adm-secret

  storage:
    storageClass: ${STORAGE_CLASS}
    size: 2Gi
    resizeInUseVolumes: True
  walStorage:
    resizeInUseVolumes: True
    size: 2Gi

  bootstrap:
    recovery:
      source: ${DB_NAME}

  replica:
    enabled: true
    source: ${DB_NAME}

  externalClusters:
  - name: ${CLUSTER_NAME}
    barmanObjectStore:
      destinationPath: "s3://${S3_BUCKET}/"
      endpointCA:
        name: "${S3_CACERT}"
        key: "cacerts.pem"
      endpointURL: "${S3_URL}"
      s3Credentials:
        accessKeyId:
          name: ${MINIO_CREDENTIALS}
          key: ACCESS_SECRET_KEY
        secretAccessKey:
          name: ${MINIO_CREDENTIALS}
          key: ACCESS_SECRET_ID
EOF
```

- Apply file 

```bash 
kubectl apply -f /tmp/cluster-restore.yaml
```


