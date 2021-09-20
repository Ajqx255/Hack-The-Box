# Vaccine
Notification: The words that are hyperlinked are to resources to learn more about the topic, protocol, or have useful information in them check them out if you want to learn more. 
# Enumeration

1. Nmap Scan 
  - First we want to start with a nmap scan and see what is available to us. to do this we will run the command below
    - `nmap -sS -A -oN nmap.txt 10.10.10.48`
    ![image](https://user-images.githubusercontent.com/29686845/133382328-9fb1414d-3ef7-440b-b5b9-96f3cbf89b49.png)
  - We see our open ports 21 FTP, 22 SSH, 80 HTTP Apache open. The main thing we notice is at the bottom of the screenshot or in our nmap scan we can see `http-title: MegaCorp Login`. This tells me that our credentials that we found in the previous machine [oopsie](https://github.com/Ajqx255/Hack-The-Box/tree/main/Oopsie) could potentially work to get us a secure shell through port 22. 
2. Port 22 SSH
  - lets try the root user that we obtained. Username: `ftpuser` Password: `mc@F1l3ZilL4` and connect with the command [SSH](https://www.openssh.com/manual.html) as seen below:
  
    ![image](https://user-images.githubusercontent.com/29686845/133384641-ee391953-ce3f-40a4-8a4e-c50499c56ed3.png)  
  - Which did not work, but it dawned on me as soon as we were denied permisson. (Dont Laugh at me... or do its fun to laugh and learn from our mistakes) there is a big hint in the name ftpuser. we have a open port and protocol that use ftp and [FileZilla](https://filezilla-project.org/) is a ftp software so we might have better luck there. 
3. Port 21 FTP
  - We will run the [FTP](https://linux.die.net/man/1/ftp) command as seen below:
    - `ftp 10.10.10.46`
    - ![image](https://user-images.githubusercontent.com/29686845/133386609-e80d051e-f851-4fe8-9552-d98a426f5aae.png)
  -  after using the same credentials as above we now see that we are succesfully connected lets see what we can find.
    - a simple `ls` command shows that there is a zip file nammed: backup.zip as seen below:
      ![image](https://user-images.githubusercontent.com/29686845/133498980-ed39df96-0c2b-4d83-adbb-9e3226339807.png)
  -  Lets extract the file back to our linux machine with using the [get](https://linux.die.net/man/1/get) command:
     - `get backup.zip`
  -  Now lets unzip the file with the command [unizp](https://linux.die.net/man/1/unzip) as seen below:
    - `unzip backup.zip`
    - When we do this we notice that it is asking for a password. Lets try the password for ftp user since it worked for usto gain access to the ftp server it might work here. 
    - ![image](https://user-images.githubusercontent.com/29686845/133500536-069549ad-1b43-4b25-8f26-cbcba8a35872.png)
    - the ftp password did not work either.
  - Since we do not know the password lets see if we can not brute force the password using [John the ripper](https://www.openwall.com/john/doc/)
    - We know we know we are going to use john to brute force the zip file, but we are going to have to do some prep work before we can actually use john. The first thing we are going to have to do is change the zip file into a format that john can read and crack. we will do this using [zip2john](https://www.hackingarticles.in/beginners-guide-for-john-the-ripper-part-2/) 
    - We will run the command `zip2john backup.zip > hash.txt`
    - ![image](https://user-images.githubusercontent.com/29686845/133515214-b0de851d-4373-407f-8421-3d5d899f124e.png)
    - John can now read teh file so lets go agead and crack it with the follwoing command:
    - `sudo john hash.txt --fork=4 -w= /usr/share/wordlists/rockyou.txt'
      - incase you were wondering --fork is stating the amount of threads or instances it will run simultaniously.
      - -w= is selecting a wordlist that you provide. In kali linux there are already wordlists install under /usr/share/wordlists. 
    -  Time frames will change based on the resources you are running, but within a matter of seconds john was able to crack to the hash:
    -  ![image](https://user-images.githubusercontent.com/29686845/133854092-b087597b-2285-4c55-bcdf-2074946b63ca.png)
      -I had issues with running this command for some reason: It worked the one time and I am glad I took a screen shot of it then. I tried multiple times to look at it and figure out what I was doing wrong, but I couldn't find it. So I would say try it out first and if it doesn't work, dont go on a three day rabbit hole like I did just move on! lol.  

