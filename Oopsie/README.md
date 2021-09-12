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
    - You may notice that the webpage is loading and not doing anything. Since we intercepted the package sent from the         website it is waiting for us to forward the packet. 
    - Now lets click on the target tab and look into the subtab Site Map
      ![image](https://user-images.githubusercontent.com/29686845/132776295-3384f904-6f9c-428a-be76-e89d22d3a205.png)
    - After looking through the files we can see the login path
    - Lets add that to our URL and see what happens. 
      ![image](https://user-images.githubusercontent.com/29686845/132776412-3e61c2d7-b1e4-412a-8d0c-8bb7a857f824.png)
    - Lets try the credentials that we learned from the previous machine. I tried both users and it do not work. 
    - After some guess work I was able to find the Username and Password. 
      - Username: Admin
      - Password: MEGACORP_4dm1n!!
    - As you will see below we got access to the Admin Panel of the website.
      ![image](https://user-images.githubusercontent.com/29686845/132804696-f989a29c-c914-4c48-aa1a-ef471ace2984.png)
    - After exploiring the webpage. we will notice something that should catch our attention in the uploads page. 
      ![image](https://user-images.githubusercontent.com/29686845/132966320-43c4b9b2-e0f8-4b45-8523-9e122cb40938.png)
    - We can see that that there is another user account: Super Admin with higher privilges that can upload files onto         the web server. 
    - Intercepting packets with burp we will notice something else interesting. when going to the accounts tab we can see       a get request the provides an id number as seen below
      ![image](https://user-images.githubusercontent.com/29686845/132966475-9d734ce7-86e5-4e02-a731-50feda6a33b1.png)
    - We will want to see if we can manipulate the value and gather enough information to get the user id of the super         admin account. We can do this with burp suites intruder mode. 
    - Press CTL + i on the packet we collected. (You will see the Intruder tab light up orange.)
    - Click on the intruder tab, make sure the subtab Target has the correct IP that we are attackin. In this case it wil       be 10.10.10.28. 
      ![image](https://user-images.githubusercontent.com/29686845/132967868-4922006e-bf25-4ef9-9e34-9510763353c5.png)
    - Click on the sub tab positions. We will see multiple selections highlighted as seen below. 
      ![image](https://user-images.githubusercontent.com/29686845/132967911-6898ddb6-e97b-48b9-b1ef-c236c685c70b.png)
    - We are only interested in the id number so lets clear the selected items with the clear tab on the right hand side       of burpsuite. 
    - highlight the id number (1) and click add.
      ![image](https://user-images.githubusercontent.com/29686845/132967985-009bb8ae-a407-48cf-b4ea-afee83f8095a.png)
    - Click the Payloads tab.
    - Now we are going to create and use a simple for loop to simplify our payload
    -     for i in `seq 1 100`; do echo $i; done
      - If you are unframiliar with scripting There are many schools of though on what programming language you should           use. I started with python and I highly recommend this book "Python Crash Course 2nd Edition..." by Eric Matthes
      - In this case we saying for i (the variable) in the sequence 1 to 100 (the condition) do echo (echo is a command           to print to the screen) $i (The $ is a wildcard) i (The variable). (That was my best attempt to expain it for             you! I am still learning myself -.-)
    - Paste the for loop into your terminal and run the command. Copy the output and past it into the list as seen below.
    
      ![image](https://user-images.githubusercontent.com/29686845/132969575-838ade83-ff1c-43b4-9d4b-e4b464b0ffb6.png)
    - Lastly click the subtab options and at the bottom make sure the section "Rediretions" says always adnd "Process             cookies in redirections" is checked
      ![image](https://user-images.githubusercontent.com/29686845/132969711-a019e061-b0b9-4ce5-9875-27feb657e8af.png)
    - Once we have everything done click the funnest button that is here.... "Start Attack". 
    - This may take some time to complete so now seems like a good time to take a break!


 
