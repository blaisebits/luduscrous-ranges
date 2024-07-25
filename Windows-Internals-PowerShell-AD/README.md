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

```git clone https://github.com/ChoiSG/ludus_ansible_roles.git
cd ludus_ansible_roles
ludus ansible role add -d ./ludus_child_domain
ludus ansible role add -d ./ludus_child_domain_join
```

# Domain Config Steps
## MINERAL Parent Domain
### PRIMARYDC
* Setup Domain / Forest
  * Install-windowsfeature ad-domain-services
  * install-addsforest -domainname mineral.local -domainnetbiosname MINERAL -installdns -force
    * we'll need a way to get a safe mode admin password
  * set-addefaultdomainpasswordpolicy -identity mineral.local -maxpasswordage 0
* Setup user accounts
  * $pwd1 = Convertto-securestring -string "password1"
  * $pwd2 = Convertto-securestring -string "password2"
  * new-aduser -name alice -country USA -accountpassword $pwd1 -givenname "Alice Bombas" -enabled $true
  * new-aduser -name bob -country JP -accountpassword $pwd2 -givenname "Bob Cordite" -enabled $true
* Setup Groups and memberships
  * New-AdGroup -Name "Local Resource" -GroupScope DomainLocal
  * Add-AdGroupMember -Identity 'Local Resource' -Members 'alice'
  * New-AdGroup -Name "Universal Group" -GroupScope Universal
  * Add-AdGroupMember -Identity 'Universal Group' -Members 'bob'
  * New-AdGroup -Name "Global Group" -GroupScope Global
  * Add-AdGroupMember -Identity 'Global Group' -Members 'alice','bob'

### CINNABAR Workstation
* ADD MACHINE TO MINERAL.LOCAL DOMAIN (Handled by range config??)
* Add-LocalGroupMember -Group 'Administrators' -Member 'MINERAL\alice'

### GRAPHITE Workstation
* ADD MACHINE TO MINERAL.LOCAL DOMAIN (Handled by range config??)
* Add-LocalGroupMember -Group 'Administrators' -Member 'MINERAL\alice'

## SALES child domain
### SALESDC
* Setup Domain
  * Set DNS to point to PRIMARYDC ??
  * Install-windowsfeature ad-domain-services
  * install-ADDSDomain -NewDomainName Sales -ParentDomainName mineral.local
    * we'll need a way to get a safe mode admin password
  * set-addefaultdomainpasswordpolicy -identity mineral.local -maxpasswordage 0
  * Validate Trust Relationship
    * Get-ADTrust -Filter * | Select Target, Direction
* Setup user accounts
  * $pwd1 = Convertto-securestring -string "password3"
  * $pwd2 = Convertto-securestring -string "password4"
  * new-aduser -name jan -country USA -accountpassword $pwd1 -givenname "Jan Brady" -enabled $true
  * new-aduser -name paul -country JP -accountpassword $pwd2 -givenname "Paul Blart" -enabled $true
* Setup Groups and memberships
  * New-AdGroup -Name "Field Reps" -GroupScope DomainLocal
  * Add-AdGroupMember -Identity 'Field Reps' -Members 'paul'
  * New-AdGroup -Name "Executives" -GroupScope Universal
  * Add-AdGroupMember -Identity 'Executives' -Members 'jan'
  * New-AdGroup -Name "Sales Group" -GroupScope Global
  * Add-AdGroupMember -Identity 'Sales Group' -Members 'paul','jan'

### GOLD Workstation
* ADD MACHINE TO SALES.MINERAL.LOCAL DOMAIN (Handled by range config??)
* Add-LocalGroupMember -Group 'Administrators' -Member 'SALES\jan'

## ENGINEERING child domain
### ENGDC
* Setup Domain
  * Set DNS to point to PRIMARYDC ??
  * Install-windowsfeature ad-domain-services
  * install-ADDSDomain -NewDomainName Engineering -ParentDomainName mineral.local
    * we'll need a way to get a safe mode admin password
  * set-addefaultdomainpasswordpolicy -identity mineral.local -maxpasswordage 0
  * Validate Trust Relationship
    * Get-ADTrust -Filter * | Select Target, Direction
* Setup user accounts
  * $pwd1 = Convertto-securestring -string "password5"
  * $pwd2 = Convertto-securestring -string "password6"
  * new-aduser -name tony -country USA -accountpassword $pwd1 -givenname "Anthony Stark" -enabled $true
  * new-aduser -name tim -country JP -accountpassword $pwd2 -givenname "Tim Taylor" -enabled $true
* Setup Groups and memberships
  * New-AdGroup -Name "Research Devs" -GroupScope DomainLocal
  * Add-AdGroupMember -Identity 'Research Devs' -Members 'tony'
  * New-AdGroup -Name "Field Repairs" -GroupScope Universal
  * Add-AdGroupMember -Identity 'Field Repairs' -Members 'tim'
  * New-AdGroup -Name "Smarty Pants" -GroupScope Global
  * Add-AdGroupMember -Identity 'Sales Group' -Members 'tony','tim'

### STEEL Workstation
* ADD MACHINE TO ENGINEERING.MINERAL.LOCAL DOMAIN (Handled by range config??)
* Add-LocalGroupMember -Group 'Administrators' -Member 'ENGINEERING\tony'
