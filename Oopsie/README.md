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

Step 2: Revieweing Ports
  - 
