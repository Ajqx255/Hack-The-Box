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
      - I tried usin burpsuite and checking the webpage source/storage to see if there were any subdirectories we can           gain access too or if there were any cookies we could exploit as we did in the machines Ooopsie & Vaccine.             There was no useful information there either. 
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
    - It should like `http://10.10.10.29/wordpress` 

# Foothold
