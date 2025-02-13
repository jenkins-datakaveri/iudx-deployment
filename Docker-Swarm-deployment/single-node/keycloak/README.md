# Introduction
Docker swarm stack for Keycloak Deployment.

## Keycloak Installation

## Docker image
A custom docker imaige based on [bitnami keycloak image](https://hub.docker.com/r/bitnami/keycloak/) includes iudx custom themes. The related files to custom keycloak image is present at docker/ dir.

Build and push the image to ghcr (if not present), using following commands:

``` 
# build docker image
docker build -t ghcr.io/datakaveri/keycloak:18.0.1-3 -f docker/Dockerfile  docker/  

# push docker image
docker push  ghcr.io/datakaveri/keycloak:18.0.1-3
```
Note: The tag is of form x.y.z-a. Where x.y.z is bitnami keycloak image version and a is UI version revision (currently 1). For each version upgrade of keycloak, tag of  base image ``bitnami/keycloak`` in docker/Dockerfile must be updated . The custom image must be built, tested and pushed to ghcr.

## Create secret files
1. To generate the passwords:
```console
./create-secrets.sh
```
2. Secrets directory after generation of secrets
```
secrets/
└── passwords
    ├── keycloak-admin-passwd
    └── keycloak-db-passwd
```
   
   
## Assign node labels

The keycloak container is constrained to run on specifc node by adding node labels to only one of the nodes, refer [here](https://docs.docker.com/engine/swarm/services/#placement-constraints) for more info. This ensures the container is placed always to same node on restart and able to mount the same local docker volume.
```sh
docker node update --label-add keycloak-node=true <node_name>
```

## Define Appropriate values of resources

Define Appropriate values of resources -
- CPU 
- RAM 
- PID limit 
in `keycloak-stack.resources.yaml`  for keycloak as shown in sample resource-values file for [here](example-keycloak-stack.resources.yaml)

## Deploy
Deploy Keycloak stack:
```sh
docker stack deploy -c keycloak-stack.yaml -c keycloak-stack.resources.yaml keycloak
```
Keycloak can be accessed at ``https://<keycloak-domain>/auth``
## Configure Keycloak
* Please refer [here](https://github.com/datakaveri/iudx-aaa-server#keycloak-setup) to do necessary keycloak configuiration for AAA server

# NOTE
1. If you need to expose the HTTP Port of keycloak or have custom stack configuration( see [here](example-keycloak-stack.custom.yaml) for example configuration of 'keycloak-stack.custom.yaml' file)  and bring up like as follows. 
```sh
docker stack deploy -c keycloak-stack.yaml -c keycloak-stack.resources.yaml -c keycloak-stack.custom.yaml keycloak
```

## ToDo
1. Make keycloak container with read-only [file-system](https://github.com/bitnami/bitnami-docker-keycloak/issues/31).
