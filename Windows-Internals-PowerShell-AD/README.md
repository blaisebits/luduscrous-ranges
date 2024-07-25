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

# Config steps
## PRIMARYDC
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

## GRAPHITE Workstation
* ADD MACHINE TO DOMAIN (Handled by range config??)
* Add-LocalGroupMember -Group 'Administrators' -Member 'MINERAL\alice'

## SALESDC
* Setup Domain
  * Set DNS to point to PRIMARYDC ??
  * Install-windowsfeature ad-domain-services
  * install-ADDSDomain -NewDomainName Sales -ParentDomainName mineral.local
    * we'll need a way to get a safe mode admin password
  * set-addefaultdomainpasswordpolicy -identity mineral.local -maxpasswordage 0


