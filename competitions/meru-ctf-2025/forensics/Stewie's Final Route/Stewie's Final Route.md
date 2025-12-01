
## Description

Thanks for helping me figure out where I was before getting lost during the maandamano - I owe you one! But the journey isn't over yet. See, I'm a man of clues - I always leave trails behind. And guess what? I left a visual clue in the first image i SENT YOU. Now that my friend helped me remember where I was, I noticed something else: he didn't just leave me a clue - he also left me the location of the t3rmin4t0rs Flash CTF! But it's conealed. He gave me a hint: "**To retrieve the file holding the location, you'll need a passphrase. The passphrase is a 10-letter word. It's the name of the place you're close to - it can be seen**"

---
## Hint

Ayy I already gave you hint... **But I look closely in the image you'll get**

---
## Solution

---

Even before we proceed, we obtain some crucial information from the description alone:

1. We will need a **passphrase** to retrieve the hidden file containing the location of the CTF.

2. The passphrase is a **10-letter word**, which is the name of a place visible in the image.

3. There is a **visual clue hidden inside the image** that hints at this passphrase.

Given these clues, it’s clear that **steganography tools like steghide or stegseek** will be needed to extract the concealed data using the passphrase.

Opening the image, I noticed it resembled the first Stewie challenge (_Where’s Stewie?_), but this time Stewie was standing. In the background, there was a lake, a tree, a flagpost, and a building. A quick reverse image search revealed it was the parliament.

![parliament](./assets/parliament.png)

Wait..., `parliament` is a perfect 10-letter word!

Without hesitation, I jumped direct to `steghide`

```bash
steghide extract -sf picnic1.jpg  
```

![steghide](./assets/steghide.png)

Alas, the passphrase `parliament` is correct. 

Trying to unzip the `secret_flag.zip` file, it prompts for a password, which I didn't know.

```bash
unzip secret_flag.zip                                              
```

![unzip1](./assets/unzip1.png)

Time to crack it. I launched `fcrackzip` with the `rockyou` wordlist:

```bash
fcrackzip -v -u -D -p /usr/share/wordlists/rockyou.txt secret_flag.zip 
```

![frackzip](./assets/fcrackzip.png)

Similarly, we can use john the ripper to crack it

```bash
zip2john secret_flag.zip > hash 

john -w=/usr/share/wordlists/rockyou.txt hash
```

We find the password as : `password`

With the password in hand, I extracted the zip, I found a file containing the flag

``` bash
unzip secret_flag.zip
```

![unzip2](./assets/unzip2.png)
 
And thus opening the file revealed the flag 

```bash
cat secret_flag.txt       
```

![flag](./assets/flag.png)

---

```
FLAG : flag{d33ply_c0nc34l3d}
```
