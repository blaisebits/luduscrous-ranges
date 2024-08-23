Range configuration for use with James Forshaw's book [Windows Security Internals](https://nostarch.com/windows-security-internals)

# LAB DIAGRAM
```
                       +---------------------------------------+                      
                       |          +---------------+            |                      
                       |          |   PRIMARYDC   |            |                      
                       |          +---------------+            |                      
                       |                                       |                      
         +------------>|                                       |<-----------+         
         | TWO-WAY     |  +---------------+  +---------------+ |    TWO-WAY |         
         | TRUST       |  |   GRAPHITE    |  |   CINNABAR    | |    TRUST   |         
         |             |  +---------------+  +---------------+ |            |         
         |             |                                       |            |         
         |             |          MINERAL.LOCAL (ROOT)         |            |         
         |             +---------------------------------------+            |         
         v                                                                  v         
 +---------------------------------------+   +---------------------------------------+
 |  +---------------+  +---------------+ |   |  +---------------+  +---------------+ |
 |  |     ENGDC     |  |     STEEL     | |   |  |    SALESDC    |  |      GOLD     | |
 |  +---------------+  +---------------+ |   |  +---------------+  +---------------+ |
 |                                       |   |                                       |
 |        ENGINEERING.MINERAL.LOCAL      |   |          SALES.MINERAL.LOCAL          |
 +---------------------------------------+   +---------------------------------------+
```

# Ludus Config Steps
## Install ansible roles
### Child domain roles

```shell
git clone https://github.com/BlaiseBits/ludus_ansible_roles.git
cd ludus_ansible_roles
ludus ansible role add -d ./ludus_child_domain
ludus ansible role add -d ./ludus_child_domain_join
```

### AD Configuration Roles
```shell
git clone https://github.com/Cyblex-Consulting/ludus-ad-content.git
ludus ansible role add -d ./ludus-ad-content
```

### Build the lab
```shell
git clone https://github.com/blaisebits/luduscrous-ranges.git
cd luduscrous-ranges/Windows-Internals-PowerShell-AD
ludus range config set -f range-config.yaml

# Deploy just the VMs and range router
# This will help reduce errors when deploying the full range
ludus range deploy -t network,vm-deploy

# Watch the ludus range logs till deploy finished before proceeding
# Deploy the full range
ludus range deploy
```