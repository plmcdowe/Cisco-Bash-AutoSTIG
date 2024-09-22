# Cisco-Bash-AutoSTIG
### Steps:
1. SCP the shell environment to flash on a Catalyst switch ( IOS 15 or 17 )
2. Ensure that `shell processing full` is globally configured
3. Send `shell environment load flash:SWFIX replace`
4. Send `pt00`
