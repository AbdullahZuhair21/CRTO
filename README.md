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

1. duble click on the payload and you will get a beacon

  ![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/1a8f88bb-1011-48da-9572-d5843562a2a1)

  ![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/ad1eda16-6efc-444f-8258-4e2c6bfce176)

- in Peer2Peer use `connect` command for TCP; use 'link' command for SMB

1. run tcp-local payload 

![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/a21c97d3-3cf6-4ed8-a8da-2cdf054649b3)

2. check if port 4444 is opened in powershell `netstat -anop tcp | findstr 4444`

![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/c16283c0-9272-4471-8e97-3695017ba286)

we will see that a new port is opened and waiting for a connection

3. connect to tcp beacon using `connect localhost 4444`

![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/47913fc7-e260-420e-928e-2eb0dd57e55f)

![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/8367e11b-00ff-465e-856c-2484af56905f)

here we just connected to ourselves in terms of understanding only

### Pivot Listener
1. To create a pivot listener, right-click on a Beacon and select `Pivoting` > `Listener`. This will open a new listener window

![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/58cd9bd4-0d90-4014-8ff3-64dd2100546d)

![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/7048e894-648e-4455-aa79-54b516b784ff)

2. after clicking save, `run netstat -anop tcp` to see your port is listening

![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/6a2a527b-0705-48d7-a379-86708943f1e6)

3. We can now generate payloads for this listener, `Payloads` > `windows Stageless Payload`.

![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/4d6577c7-07c2-4922-b9c7-a675a3188da1)

choose the pivot listener and generate the payload and save it in Payloads folder

![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/532aadf2-5b22-4452-8ee0-01ce88627e97)

4. run the payload, and you will get a new beacon

![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/4322c4ec-5cc7-498c-bb34-2bed1812b99b)

![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/aa29e7be-72cd-4981-b172-6d5c0d5879a2)

5. check `spawn`, `elevate`, and `jump` commands.

### Running as a service
1. create a `teamserver.service`file in `/etc/systemd/system`
```
attacker@ubuntu ~> sudo nano /etc/systemd/system/teamserver.service
```
2. paste the following content
```
[Unit]
Description=Cobalt Strike Team Server
After=network.target
StartLimitIntervalSec=0

[Service]
Type=simple
Restart=always
RestartSec=1
User=root
WorkingDirectory=/home/attacker/cobaltstrike
ExecStart=/home/attacker/cobaltstrike/teamserver 10.10.5.50 Passw0rd! c2-profiles/normal/webbug.profile

[Install]
WantedBy=multi-user.target
```
3. Next, reload the systemd manager and check the status of the service.  It will be inactive/dead.
```
attacker@ubuntu ~> sudo systemctl daemon-reload
attacker@ubuntu ~> sudo systemctl status teamserver.service
```
4. Start the service and check its status again.
```
attacker@ubuntu ~> sudo systemctl start teamserver.service
attacker@ubuntu ~> sudo systemctl status teamserver.service
```
5. Start the service on bootup.
```
attacker@ubuntu ~> sudo systemctl enable teamserver.service
```



# External Reconnaissance
### DNS Records
1. Performing DNS lookup of any `A` records for the domain
```
dig cyberbotic.io
```
![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/e31d41b8-b830-44cd-af8b-68c029d0c2f5)

2. Performing a `whois` on each public IP address can show who it belongs to. We can see that it resolves to a 3rd party provider, Cloudflare.
```
whois 172.67.205.143
```
![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/6f8687e5-a382-4629-ab08-191490bcf0fd)

3. brute force subdomain using `dnscan`
```
./dnscan.py -d cyberbotic.io -w subdomains-100.txt
```
![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/f6f451a0-a062-4a03-90e5-9509532238b4)

From the output above, we've discovered www and mail subdomains. if you notice that mail resolves to an internal address rather than a public address

4. (Spoofy)[https://github.com/MattKeeley/Spoofy] is a Python tool that can verify the email security of a given domain.

![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/06f308e6-72b1-4a1b-b29d-4cf7acdec0ae)

#### Google Dorks




### Social Media


































