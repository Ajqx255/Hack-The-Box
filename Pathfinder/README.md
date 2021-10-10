# Pathfinder

Hello All, Today we are going to be looking at the HTB machine Pathfinder. As always I hope you this walk trhough will help you learn and understand the concepts of what we are doing. I will provide links for certian protocols or concepts, and I highly recommend you read/learn more about it. Beleieve me I am right there with you. This is a new journey for me too but putting in the work now will only maks us better! I hope you enjoy and lets get hacking. 

# Enumeration: 

- Step 1: NMAP Scan
  - We are going to run the command `nmap -sS -A -oN NAMP.txt. 10.10.10.30`
    ![image](https://user-images.githubusercontent.com/29686845/136679722-9e4ed0f7-3b0a-4277-a8b7-24152feae9b2.png)
  - As seen above this provides quite a bit of output for us to review. I will break it down for us by port below:
    - Port 53/TCP [Simple DNS Plus](https://simpledns.plus/): Simple DNS is a user/beginner friendly option to setting up a [DNS](https://www.cloudflare.com/learning/dns/what-is-dns/) server. It uses/configures all Security settings, zones, data, and updates within the Windows user interface. This (again) allows new users to spin up properly running and secure DNS server and also allows expernced uses to modify/change the [DNS server](https://www.cloudflare.com/learning/dns/what-is-a-dns-server/) in anyway they see fit. 
