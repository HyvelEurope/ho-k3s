![GitHub License](https://img.shields.io/github/license/tengzl33t/k3s-ho)

# HyvelOne k3s Cluster

This repository contains the declarative configuration of HyvelOne k3s cluster.

## Components

### Networking

- [Cilium](https://github.com/cilium/cilium) - Replaces flannel in default k3s configuration.
  Allows to get real client IPs for deployed applications.
  Connected with OpenWRT router via BGP to get reliable LB IP pool.
- [cert-manager](https://github.com/cert-manager/cert-manager) -
  Provides certificates for internal and external services.
- [Traefik](https://github.com/traefik/traefik) - Ingress (reverse-proxy/LB)

### Security

- [anubis](https://github.com/TecharoHQ/anubis) - PoW middleware to stop automatic bots/crawlers
- [lldap](https://github.com/lldap/lldap) - Lightweight LDAP implementation
- [authelia](https://github.com/authelia/authelia) - SSO for services

### Public services

- [vaultwarden](https://github.com/dani-garcia/vaultwarden) - [Bitwarden](https://github.com/bitwarden/server) server
  Rust-based implementation
- [3X-UI](https://github.com/MHSanaei/3x-ui) - [Xray](https://github.com/XTLS/Xray-core) server with web panel
- [Immich](https://github.com/immich-app/immich) - photo management/gallery app

# Structure graphs

## Authelia

```mermaid
flowchart LR
    subgraph s1["Traefik"]
        n4["forwardAuth Middleware"]
    end
    n1["Authelia"] --> n2["LDAP"]
    n3["Immich"] --> n1
    n4 --> n1
    n5["Proxmox VE"] --> n1
    n6["Internal Services"] --> n4
```

## Network

```mermaid
flowchart LR
    subgraph s1["Router"]
        n4["Bird3 BGP Peer"]
        n15["DDNS"]
        n17["DHCP"]
    end
    subgraph s2["Cilium"]
        n10["BGP Peer"]
        n16["LB IP Pool"]
    end
    subgraph s3["k3s Cluster"]
        n11["Traefik Ingress"]
        n12["LB Services"]
        n13["Deployments"]
        n14["Other"]
    end
    n1["Proxmox VE Server"] --> n2["x-k3s-server VM"]
    s1 --> n3["Internet"]
    n10 <--> n4
    n2 --> s3
    n12 --> n16
    n11 --> n16
    n2 <--> n17
```

## Backup

```mermaid
flowchart LR
    subgraph s1["y-int VM"]
        A(["Minio"])
    end
    subgraph s2["k3s Cluster"]
        C["k8up Schedules"]
        D["PVCs"]
        n1["Deployments"]
    end
    subgraph s3["k3s VMs"]
        n2["x-k3s-server system volume"]
    end
    n1 --> C
    D --> C
    C --> A
    n2 --> n3["Proxmox VE Server"]
```

Copyright (c) tengzl33t  
This work is licensed under a Creative Commons Attribution-ShareAlike 4.0 International License (CC BY-SA 4.0).  
See [LICENSE-CONF](LICENSE-CONF) for details.