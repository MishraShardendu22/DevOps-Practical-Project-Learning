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

## Network Drivers Explained

### Bridge (Default)
- Creates private internal network on host
- Containers can communicate via IP or container name
- Isolated from host network
- Best for standalone containers on single host

### User-Defined Bridge
- Custom bridge network with enhanced features
- Automatic DNS resolution by container name
- Better isolation control
- Can be connected/disconnected on the fly

### Host
- Container shares host's network stack
- No network isolation
- Better performance (no NAT overhead)
- Use for performance-critical applications

### None
- Completely disables networking
- Maximum isolation
- Use for security-sensitive containers that don't need network access

## Advanced Commands

### Connect Container to Network
```bash
docker network connect <network-name> <container-name>
```

### Disconnect Container from Network
```bash
docker network disconnect <network-name> <container-name>
```

### Remove Network
```bash
docker network rm <network-name>
```

## Best Practices

- Use user-defined bridge networks for better container isolation and DNS
- Use host network sparingly (only for performance-critical applications)
- Use none network for security-sensitive containers that don't need network access
- Name your networks descriptively for easier management
- Clean up unused networks regularly with `docker network prune`
