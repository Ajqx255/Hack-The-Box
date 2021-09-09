# Hack the Box: Oopsie

# Enumeration 

Step 1: Nmap Scan
  - First we are going to run a nmap scan
    -       sudo nmap -sS -A 10.10.10.28

![image](https://user-images.githubusercontent.com/29686845/132744067-062cdd8e-440a-4a93-955c-a93a49bf5db0.png)

  - Looking at the scan we can see that port 22 ssh and port 80 http are open. 
    - Port 22 SSH: Is a networ protocol that allows a secure connection to a computer or server. 
      - Check out the link below if you wan to learn more
      - https://searchsecurity.techtarget.com/definition/Secure-Shell
    - Port 80 http: http is an application level protocl that allows communication between web browsers and web servers.       This port is using apache one of the worlds most popular web server software.
      - If you would like to read more about http and apache check out the links below!
      - https://developer.mozilla.org/en-US/docs/Web/HTTP/Overview
      - https://httpd.apache.org/
     
Step 2: Open Ports
  - First we will look at port 22
    - This will lead nowhere, but for the sake of realism we will go through the steps. If you would like to jump ahead         feel free to do so. 
    - We will try to connect to ssh with the user and administrator credentials we have gathered from the previous box         archetype.  
    -     ssh ARCHETYPE/sql-svc@10.10.10.28
    -     ssh administrator@10.10.10.28
    - As you can see in the screen shots below we have been denied access

      ![image](https://user-images.githubusercontent.com/29686845/132754134-8397a004-482f-4dd5-a56f-ba76c495c538.png)

      ![image](https://user-images.githubusercontent.com/29686845/132754201-8f167c3e-0167-4436-a468-010bd8470afd.png)

  - Now lets take a look at Port 80
    - We will do this by opening a web browser and typing the IP Address of the machine into the url bar
    -     http://10.10.10.28
