### Abstract

Helm chart to deploy a Boundary controller and its Postgres back end with optional PVC in either enterprise or dev mode; includes Vault sub chart for optional dev server/Agent injector.

### Prerequisites

- Minikube, Kind, etc, cluster running

- Helm installed


### Procedure

- clone & cd into repo

- have Minikube or Kind cluster running

- if Vault/Boundary enterprise functionality desired, save licenses to K8s secrets

- from within directory, run `helm install boundary .`

- kubectl get pods will now show controller and Postgres back end 

- Postgres PVC disabled by default

- dev mode enabled by default 

- when not in dev mode, must go through recovery KMS workflow to set up users, accounts, targets, etc

#### Example Non-Ent Installation
```
git clone https://github.com/clay-codes/boundary-helm.git
cd boundary-helm
helm install boundary .
kubectl get pods
```

### Additional Configurations

#### **Enterprise:**

- disabled by default

- create K8s Boundary license secret

- set lines 14 and 15 to the k8s secret name and key of your Boundary license, or use inline method below

- (optional) set line 24 `boundary.setup.dev=false` to disable enterprise dev mode 

- behind the scenes, helm will change image to enterprise once line 14 is set

- if not already installed, run `helm install boundary .` from within directory

- if already installed, either uninstall and re-install with updated values, or run through db migration below to avoid uninstall

**Example Inline Installation**
```
kubectl create secret generic boundary-license --from-literal=license=${BOUNDARY_LICENSE}
helm install boundary . --set boundary.image.licenseSecretName=boundary-license
```

#### **Vault**

- disabled by default

- sub-chart of official Vault helm

- dev mode supported, enabled by default

- injector supported, disabled by default

- all official features technically available, but not tested

- set `vault.server.enabled=true`

- for enterprise, set `vault.server.enterpriseLicense.secretName=<your_k8s_vault_secret>`

- for injector, set `vault.injector.enabled=true`

__Example Vault-Ent Dev Installation__
```
kubectl create secret generic vault-license --from-literal=license=${VAULT_LICENSE}
helm install boundary . --set vault.server.enabled=true,vault.server.enterpriseLicense.secretName=vault-license
```

#### __DB migration:__

- turn off dev mode

- (optional) enable PVC `postgres.pvc.enabled=true`

- migration required to upgrade Boundary version, for root KMS migration, any mod to config, etc

- install boundary: `helm install boundary .` or use inline method below if testing migration via boundary version upgrade 

- modify config: below example tests migration by upgrading versions, but can also test by modifying `boundary.config`

- ensure `--set boundary.setup.migrateDB=true` flag is in final upgrade command (or override file)

**Example Inline Boundary Upgrade**
```
helm install boundary . --set boundary.image.tag=0.15.4,boundary.setup.dev.enabled=false
k get pod boundary-0 -oyaml | grep image
helm upgrade boundary . --set boundary.image.tag=latest,boundary.setup.migrateDB=true
```

#### **Features Coming Soon:**

- worker node

- upstream workers
