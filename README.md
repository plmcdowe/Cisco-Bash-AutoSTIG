# Cisco-Bash-AutoSTIG
>### **-- Production IPs have been replaced with *ip.ip.ip.ip*;**
>### **-- Where regex is used, production IPs have been replaced with some variation of *123.45.67.89*;**
>### **-- Production keys and passwords have been replaced with descriptive strings;**
>### **-- Production vlans have been replaced;**
### Script Actions:
- Enables `service password-encryption` and removes `no password encryption aes`
  - We had issues with Radius & TACACS keys properly exchanging when Types 8 or 9.
  
- Enables SCP.
- Applies `aaa common-critera policy`.
- Removes local accounts configured with `password` instead of `secret` (Type-7 hash instead of Type-9).
- Removes local accounts that are not the authorized *account-of-last-resort*.
- Configures the authorized local account with `secret` associated to the `aaa common-critera policy`.
- Removes Radius `aaa group server radius`.
- Removes `radius dynamic author`.
- Reconfigures Radius & TACACS servers globally.
- Removes management ACL from the management vlan interface.
- Globally removes the management ACL.
- Globally removes other ACLs as necesary (in this example, *SNMP* and *SSH*).
- Globally reconfigures the *SNMP* and *SSH* standard ACLs.
- Removes SNMP users.
  - I had to clean up alot of old views and users.
  - To fully remove an SNMP user, the switch must be reloaded after `no snmp-server` (once the script is done).
    
- Creates object-groups for use in ACLs, *AAA* in this example.
- Globally reconfigures the management ACL.
- Reapplies the management ACL to the managemnet vlan interface.
- Reapplies `aaa new-model` *authentication*, *authorization*, and *accounting*.
- Reconfigures VTY lines.
- Configures "quiet mode" `login block-for` and associates `login quiet-mode access-class` to the *SSH* ACL so that hosts in those networks can still SSH into the switch when in quiet mode.
- Miscellaneous STIG items such as disabling non-secure and un-used features.
- Reconfigures FIPS compliant SSH algorithms for IOS 15 & 17 to later enable FIPS mode.
- For IOS 15 & 17:
  - Configures appropriate device tracking;
  - Configures logging. If IOS 17, configures for RFC 5424 for TCP syslog instead of Cisco default RFC 3164;
  - Finds, defaults, and applies *dot1x access* interface config to trunk interfaces that are not in use;
  - Iterates access interfaces, removing unauthorized & unnessary lines;

- Globally configures spanning-tree without priority.
  - The administrator is prompted at the end of the script to verify & configure spanning-tree global priority if root; `spanning-tree guard root` on appropriate trunks.
    
- Removes all posible `vlan-lists` for any combination of vlan IDs.
- Finds all globally configured vlans, then removes all that are not *management*, *access default*, *trunk native* IDs.
- Checks the default gateway to apply site-specific ordering of Radius servers when reconfiguring `aaa group server radius` and `radius dynamic author`
- Applies vlan globally and in trunks based on site-specific requirements determined from the default gateway.
- Updates NTP authentication keys based on IOS version.
- Echos out a modified version of `sh cdp ne detail` for each interface to aid in correctly applying `spanning-tree guard root` and determininng if the switch is root to set priority on `spanning-tree vlan 1-4094`.
### Steps to run:
1. SCP the shell environment to flash on a Catalyst switch ( IOS 15 or 17 )
2. Ensure that `shell processing full` is globally configured
3. Send `shell environment load flash:SWFIX replace`
4. Send `pt00`
5. Reload the switch if SNMP users were removed and the SNMP feature was disabled with `no snmp-server`.
6. Once the switch has reloaded, apply the current SNMP configuration, and `wr`.
