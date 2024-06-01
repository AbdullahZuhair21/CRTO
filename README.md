# CRTO
# COMMAND & CONTROL
### Starting and connecting to the Team Server
1. run `team server SSH` > type `tmux` in the terminal > cd cobaltstrike > `sudo ./teamserver <IP_Attacker> <Password> c2-profiles/normal/webbug.profile`

![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/c857de3c-ea19-40da-8247-73bcf9309cb7)

2. launch the client

![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/2127a7ea-a1e9-4f71-82de-eb764dc8ea30)

3. make sure that both of the fingerprint hashes are the same

![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/64c9d4e8-7bd4-4a46-9e30-ffa0efae2ed9)

### Listerner Management
.    Gress Listener uses HTTP/S & DNS; Peer2Peer users SMB & raw TCP
.    use `sudo ss -lntp` to check the listing ports
1. http listener

![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/4cc00aae-bcc2-4344-a678-a14075a29dd2)

2. DNS listener

![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/95028a42-a826-459d-a66f-e746d716a8b2)

.    test the DNS listener using `dig @ns1.nickelviper.com test.pics.nickelviper.com +short`. The team server's default response is 0.0.0.0

![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/fb4b98c6-093c-4adb-a4d5-67d8db8c07a6)

3. SMB listener
.    beacon SMB payload will start a new named pipe server but the default pipe name is quite well signatured by EDRs. instead, use the following command `ls \\.\pipe` in powersehll. Take any pipe name and rename the last 4 letters to make it unique

![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/80a5f0a7-c513-4d21-aaba-a733b9ae069c)

![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/4808fd11-337e-41f8-8f01-3b8775805abd)

4. TCP
.    you may specify whether it will bind to only the localhost (127.0.0.1), otherwise, it will bind to all interfaces (0.0.0.0).

![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/e314926e-c8d7-4dba-85f8-ebd5fde25318)

5. Below are the full listeners that we are going to use

![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/ffb3df80-72bd-44a8-b099-d0a52774bd51)

### Generating Payloads
- in gress payload you will directly interact with the beacon

  .    duble click on the payload and you will get a beacon

  ![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/1a8f88bb-1011-48da-9572-d5843562a2a1)

  ![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/ad1eda16-6efc-444f-8258-4e2c6bfce176)

- in Peer2Peer
.    check if port 4444 is opened in powershell `netstat -anop tcp | findstr 4444`
  
.    use `connect` command for TCP

.    use 'link' command for SMB
