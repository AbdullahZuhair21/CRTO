# Certified Red Team Operator (CRTO) - Cheatsheet

# Host Privilege Escaltion
```powershell
- Unquoted Service Path
1. run WMI to pull a list of every service and the path to its executable
beacon> run wmic service get name, pathname

```
