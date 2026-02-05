# Docker Network

## Overview

Docker networking allows containers to communicate with each other and with external networks.

## Network Types

Docker supports 7 types of networks (Driver Name's):

1. **host** - Container shares the host's network stack
2. **none** - Container has no network access
3. **bridge** - Default network driver (isolated network)
4. **user defined bridge** - Custom bridge network with enhanced features
5. **macvlan** (docker swarm) - *outdated*
6. **ipvlan** - *outdated*
7. **overlay** - *outdated*

## Network Commands

### List Networks

```bash
docker network ls
```

### Create Network

```bash
docker network create <network-name> -d <driver-name>
```

### Inspect Network

```bash
docker network inspect <network-name>
```

## Best Practices

- Use user-defined bridge networks for better container isolation
- Use host network for performance-critical applications
- Use none network for security-sensitive containers that don't need network access

```md

# Search for Networks

mishrashardendu22@fedora:~$ docker search mongo
NAME                               DESCRIPTION                                     STARS     OFFICIAL
mongo                              MongoDB document databases provide high avai…   10693     [OK]
circleci/mongo                     CircleCI images for MongoDB                     16        
corpusops/mongo                    https://github.com/corpusops/docker-images/     0         
rootpublic/mongo                                                                   0         
uselagoon/mongo                                                                    0         
ekesken/mongo                      docker image for mongo that is configurable …   1         
vepo/mongo                         Mongo image with default settings               1         
litmuschaos/mongo                                                                  1         
jelastic/mongo                     An image of the MongoDB database server main…   0         
arm64v8/mongo                      MongoDB document databases provide high avai…   37        
zinobe/mongo                       mongo with custom user                          0         
s390x/mongo                        MongoDB document databases provide high avai…   1         
kubedb/mongo                                                                       0         
amd64/mongo                        MongoDB document databases provide high avai…   0         
cescoferraro/mongo                 docker alpine mongo                             0         
ccitest/mongo                      CircleCI test images for Mongo                  0         
sacashgit/mongo                                                                    0         
excellalabs/mongo                  Dockerfile and scripts to setup a production…   2         
winamd64/mongo                     MongoDB document databases provide high avai…   1         
mongodb/mongodb-community-server   The Official MongoDB Community Server           176       
healthcheck/mongo                  https://github.com/docker-library/healthchec…   5         
mongodb/mongodb-atlas-local        Create, manage, and automate MongoDB Atlas L…   10        
pagarme/mongo                                                                      0         
pastvu/mongo                                                                       0         
sscpac/mongo                       alpine mongo                                    0         
mishrashardendu22@fedora:~$ 


```
