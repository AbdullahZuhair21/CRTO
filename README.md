# CRTO
# COMMAND & CONTROL
### Starting the Team Server
From the console of the Attacker Desktop, right-click on the Terminal icon in the taskbar and select Team Server.  This will SSH you into the Attacker Linux VM.

![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/91f54a06-bb5c-4fd1-be4b-2ebc95ad7e23)

From the command prompt, move into the `cobaltstrike` directory and run the teamserver executable.
```
attacker@ubuntu ~/cobaltstrike> sudo ./teamserver <Attacker_IP> <passwd> c2-profiles/normal/webbug.profile (example of Malleable C2 profile)
```
where:
.    
