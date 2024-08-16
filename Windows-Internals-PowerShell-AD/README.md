Range configuration for use with James Forshaw's book [Windows Security Internals](https://nostarch.com/windows-security-internals)

This configuration is in development, but will meet the requirements layed out in Appendix A.

**THIS LAB IS IN DEVELOPMENT AND NOT CURRENTLY FUNCTIONAL**

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

```
git clone https://github.com/ChoiSG/ludus_ansible_roles.git
cd ludus_ansible_roles
ludus ansible role add -d ./ludus_child_domain
ludus ansible role add -d ./ludus_child_domain_join
```

### AD Configuration Roles
```
git clone https://github.com/Cyblex-Consulting/ludus-ad-content.git
ludus ansible role add -d ./ludus-ad-content
```
