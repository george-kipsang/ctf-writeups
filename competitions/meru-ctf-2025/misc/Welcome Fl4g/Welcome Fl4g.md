
## Description

As a curious web crawler, you’ve landed on the t3rmin4t0rs CTF server.
But not everything is visible at first glance… some things are hidden in plain sight.
Can you find the first flag the admins didn’t want bots to see?

---
## Solution

When exploring any website, one of the first places a curious web crawler—or anyone interested in reconnaissance—should check is the `robots.txt` file. 

This is a simple text file located in the root directory of a website that serves as a set of instructions for web crawlers or bots. Its primary purpose is to guide these bots on which parts of the site they are allowed or disallowed to access and index. 

While it doesn’t enforce access restrictions, well-behaved bots usually respect these directives to avoid crawling sensitive or irrelevant sections of the site.

Knowing that many websites use a `robots.txt` file to guide web crawlers, I navigated to

```
http://<target-server>/robots.txt
```

Opening the `robots.txt` file, I found a suspicious string that looked like `Base64` encoded data rather than the usual disallow rules.

I copied the string 

```
ZmxhZ3t3M2xjMG0zX3QwX3Qzcm1pbjR0MHJzX2ZsNHNoX2N0Zn0=
```

and decoded it using the command:

```bash
echo "ZmxhZ3t3M2xjMG0zX3QwX3Qzcm1pbjR0MHJzX2ZsNHNoX2N0Zn0=" | base64 -d
flag{w3lc0m3_t0_t3rmin4t0rs_fl4sh_ctf} 
```

---

```
FLAG : flag{w3lc0m3_t0_t3rmin4t0rs_fl4sh_ctf}
```
