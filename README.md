# Certified Red Team Operator (CRTO) - Cheatsheet

# Host Privilege Escaltion
```powershell
1. Unquoted Service Path
# run WMI to pull a list of every service and the path to its executable
beacon> run wmic service get name, pathname

```
