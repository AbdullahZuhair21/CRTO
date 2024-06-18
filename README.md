# Certified Red Team Operator (CRTO) - Cheatsheet

# Host Privilege Escaltion
```powershell
- [Unquoted Service Path]
1. run WMI to pull a list of every service and the path to its executable
beacon> run wmic service get name, pathname
2. check if you have write permission
beacon> powershell Get-Acl -Path "C:\Program Files\Vulnerable Services" | fl
```
