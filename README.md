# MongoDB backup helm chart

This helm chart will deploy a cronjob that will perform the backup of a MongoDB instance on a persistent volume.
It can optionally configure also a stash backup of the persistent volume.

# Requirements

helm 3

# helm repository

First you need to add Plenus helm repository

```
helm repo add plenus https://plenus-charts.storage.googleapis.com/stable/
helm repo update
```

# Usage

At the bare minimum you need to configure the values:
- envs.MONGOHOST
- envs.MONGOUSER
- envs.MONGOPASSWORD
with the service, username and password used to access mongodb.

Check also pvc.class, set it to a storage class present on your cluster.

```
helm install mongodb-backup plenus/mongodb-backup --namespace=my-namespace --set envs.MONGOHOST=mymongodb --set envs.MONGOUSER=root --set envs.MONGOPASSWORD=rootpass --wait --atomic
```

To configure more parameters (ex. schedule or stash) use a values files, for example:

```
cat <<EOF > values-mongodb-backup.yaml
cronjob:
  schedule: "03 2 * * *"

envs:
  MONGODUMP: "mongodump"
  MONGOHOST: "mymongodb"
  MONGOUSER: "root"
  MONGOPASSWORD: "rootpass"

stash:
  active: true
  schedule: "00 3 * * *"
  retention:
    keepLast: 30
  swift:
    active: true
    osAuthUrl: "https://auth.cloud.ovh.net/v3/"
    osProjectDomainName: "Default"
    osProjectName: ""
    osRegionName: "DE"
    osUserDomainName: "Default"
    osUsername: "myswiftuser"
    osPassword: "myswiftpassword"
    container: "mycontainer"
    prefix: "my/prefix"
  resticPassword: "changeme"
EOF

helm install mongodb-backup plenus/mongodb-backup --namespace=my-namespace --values values-mongodb-backup.yaml --wait --atomic
```
