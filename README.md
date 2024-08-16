# NetSec Challenge by THM (Write-Up) 

by politelychaotic
July 15, 2024

## Task 1: Start up the Attackbox/VM of Kali and the target VM

## Task 2: Challenge Questions
The following can be answered using: Nmap, Telnet, and Hydra.

`Target IP: 10.10.88.49`


### Question 1: What is the highest port number being open less than 10,000?

Command: `nmap -p1-10000 -T4 MACHINE_IP`
+ `p1-10000`: scans ports between 1 and 10,000 (since it was specified, we are looking for the last open port that has a value of less than 10,000)
+ `T4`: is for aggressive scanning and is used in CTFs and for practice/learning generally

<img width="468" alt="image" src="https://github.com/user-attachments/assets/1a08d094-3b7d-414f-b757-cf71db8246a9">

>Answer: `port 8080`

### Question 2: There is an open port outside the common 1000 ports; it is above 10,000. What is it?

Command: `nmap -p10000-65535 10.10.88.49`

<img width="468" alt="image" src="https://github.com/user-attachments/assets/5398e633-7367-4f51-9533-312509b6e7b2">

>Answer:  `10021`

### Question 3: How many TCP ports are open?

>Answer: `6`

### Question 4: What is the flag hidden in the HTTP server header?

+ Command: `telnet 10.10.88.49 80`
+ Then: `GET / HTTP/1.1` (to get default page)
+ Finally: `host: telnet`

<img width="468" alt="image" src="https://github.com/user-attachments/assets/9e4bd7b7-385b-40e4-929a-79484e047e7b">

>Answer: `THM{web_server_25352}`

### Question 5: What is the flag hidden in the SSH server header?

Command: `telnet 10.10.88.49 22`

<img width="340" alt="image" src="https://github.com/user-attachments/assets/f441e845-47c1-42ed-bc61-58cea1e9a6c0">

>Answer: `THM{946219583339}`

### Question 6: We have an FTP server listening on a nonstandard port. What is the version of the FTP server?

+ I think it is port 10021, since there is no service listed on the nmap scan, but it does show as open. Let’s try to connect with telnet:

Command: `telnet 10.10.88.49 22 10021`

<img width="340" alt="image" src="https://github.com/user-attachments/assets/24f0a1bf-1ea7-48e1-a554-73a937d6aa64">

>Answer: `vsFTPd 3.0.3`


### Question 7: We learned two usernames using social engineering: eddie and quinn. What is the flag hidden in one of these two account files and accessible via FTP?

Let’s use hydra and rockyou.txt to try to gain access to these accounts over FTP.

+ For eddie: hydra -l eddie -P /usr/share/wordlists/rockyou.txt  ftp://10.10.88.49:10021 (since it is non-standard port for ftp we have to specify port)

>Password found: `jordan`

<img width="468" alt="image" src="https://github.com/user-attachments/assets/af221b7b-bc7f-44ac-ba0c-9ba3d9bffce9">

+ For quinn: `hydra -l quinn -P /usr/share/wordlists/rockyou.txt  ftp://10.10.88.49:10021`

>Password found: `andrea`

<img width="468" alt="image" src="https://github.com/user-attachments/assets/217abf79-e517-4c52-a999-c686b3313c51">

+ Logging in as **_eddie_** revealed there were no files in the directory, but **_quinn_** had the file: `ftp_flag.txt`.
To grab this file: `get ftp_flag.txt`

<img width="468" alt="image" src="https://github.com/user-attachments/assets/2449b17a-ee02-4059-aa92-f71638adad54">

>Answer: `THM{321452667098}`

### Question 8: Browsing to http://10.10.88.49:8080 displays a small challenge that will give you a flag once you solve it. What is the flag?

+ Let’s try running a Null scan “-sN” option, to reduce likelihood of being detected because it does not set a TCP flag. This also means we can potentially bypass firewalls that block the TCP SYN flag.

Command: `nmap -sN TARGET_IP`

<img width="468" alt="image" src="https://github.com/user-attachments/assets/4e1dc729-752b-479a-9ac2-98ac1eae8f45">

>Answer: `THM{f7443f99}`




