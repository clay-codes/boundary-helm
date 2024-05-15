# Boundary Helm Chart

### Installation
- clone repo
- (optional) set boudary license: 
`kubectl create secret generic boundary-license --from-literal=license=${BOUNDARY_LICENSE}`
- (optional) set vault license: `kubectl create secret generic vault-license --from-literal=license=${VAULT_LICENSE}`
- from within directory, run `helm install boundary .`
- k get po will show enterprise-dev boundary server + postgress back end

### Additional Options

#### Production Mode
- by default, dev is enabled
- can set `dev.enabled=false` to provision without generated resources

#### Vault
- Vault subchart is also included
- set `vault.server.enabled=true` to spin up a vault dev server alongside
- set `vault.injector.enabled=true` for vault agent injector pod

#### PVCs
- set `postgres.pvc.enabled=true` to test migrate db/kms functionality
- must be initialized with PVCs to test migration