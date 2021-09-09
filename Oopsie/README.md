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
    - At first glance there seems to be nothing of value on the web page, but after reading through it we notice               something important under services. It references a customer login.
      
      ![image](https://user-images.githubusercontent.com/29686845/132760926-860112b0-c9ad-4d96-972c-c29457782e18.png)

  - Next we will us burp suit to look furhter into the backend of the webpage. First I am going to show you how to set up     the proxy burp suite will need (If you have not already) in the next step. 

Step 3: Burp Suite
  - We will need to set up the foxy proxy standard extenision within you firefox browser.
    - To do this we click on the three lines on the top left of your browser, click on add ons, search for "foxy proxy",       click into foxy proxy standard, and click add to firefox. 
  - Open burp suite and create a temporary new project. 
  - Once you are into burp you will need to configure your proxy to run through foxy proxy.
    - Start by going to the proxy tab in burpsuite, click options in the subtab, and look at your Proxy Listeners.
      ![image](https://user-images.githubusercontent.com/29686845/132773770-95494d79-9325-45a3-94ac-3b3806ba7da3.png)
    - Take note of the Interface IP that is listed it should look like the one in the snapshot above. 
    - Now go to your foxy proxy icon on firefox and open it by clicking the icon, click options and configure foxy proxy       with the interface ip and port number that was in burpsuite.
      ![image](https://user-images.githubusercontent.com/29686845/132774125-3767037e-b934-49e8-aec3-17050de88ee4.png)
    - Lastly click save.
  - Now that we have everything configured. We are going to use Burp to intercept, change, and modify packets that are       going from the web page to the web server and see what we can access.
    - click on you foxy proxy icon and click on your new proxy and refresh the page.
    - You will see that in burp suite the proxy tab and the intercept subtab both turned orange. If we look into               intercept we will see the packet that was just sent to refresh the page. 
      ![image](https://user-images.githubusercontent.com/29686845/132775431-0bced0eb-1f7b-4190-9077-20a58d6b8161.png)

