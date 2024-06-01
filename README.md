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
.    beacon SMB payload will start a new named pipe server but the default pipe name is quite well signatured. instead, use the following command `ls \\.\pipe` take any pipe name and rename the last 4 letters to make it unique
![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/4808fd11-337e-41f8-8f01-3b8775805abd)

5. TCP

6. 
