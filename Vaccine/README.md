# Vaccine
Notification: The words that are hyperlinked are to resources to learn more about the topic, protocol, or have useful information in them check them out if you want to learn more. 
# Enumeration

1. Nmap Scan 
  - First we want to start with a nmap scan and see what is available to us. to do this we will run the command below
    - `nmap -sS -A -oN nmap.txt 10.10.10.48`
    ![image](https://user-images.githubusercontent.com/29686845/133382328-9fb1414d-3ef7-440b-b5b9-96f3cbf89b49.png)
  - We see our open ports 21 FTP, 22 SSH, 80 HTTP Apache open. The main thing we notice is at the bottom of the screenshot or in our nmap scan we can see `http-title: MegaCorp Login`. This tells me that our credentials that we found in the previous machine [oopsie](https://github.com/Ajqx255/Hack-The-Box/tree/main/Oopsie) could potentiall work to get us a secure shell through port 22. 
2. Port 22 SSH
  - lets try the root user that we obtained. Username: `ftpuser` Password: `mc@F1l3ZilL4` and connect with the command [SSH](https://www.openssh.com/manual.html) as seen below:
 
![image](https://user-images.githubusercontent.com/29686845/133384641-ee391953-ce3f-40a4-8a4e-c50499c56ed3.png)  
  - Which did not work, but it dawned on me as soon as we were denied permisson. (Dont Laugh at me... or do its fun to laugh and learn from our mistakes) there is a big hint in the name ftpuser. we have a open port and protocol that use ftp and [FileZilla](https://filezilla-project.org/) is a ftp software so we might have better luck there. 
3. Port 21 FTP
  - Well run the [FTP](https://linux.die.net/man/1/ftp) command as seen below:

    ![image](https://user-images.githubusercontent.com/29686845/133386609-e80d051e-f851-4fe8-9552-d98a426f5aae.png)
