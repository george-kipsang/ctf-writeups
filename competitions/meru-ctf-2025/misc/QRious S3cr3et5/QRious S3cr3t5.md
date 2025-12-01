## Description

A mysterious QR code has been found printed on a sticky note at a hacker's desk. It seems like just an ordinary QR code... or is it?

---
## Solution

The challenge had 2 downloadable files. First, I tried extracting the zip, but it was password protected. I attempted cracking it with `fcrackzip` and `john the ripper`—no luck

So, I turned to the QR code. On Linux, I used `zbarimg` to scan it

```bash
zbarimg challenge_qr.png              
```

![zbarimg](./assets/zbarimg.png)

```
output : ++++++++++[>+>+++>+++++++>++++++++++<<<<-]>>>>.+.+++++++++++.<<++++++++++++++++++.>>+.<<+++.
```

This looked like **Brainfuck** code from a previous CTF I did. I decoded it on [dcode.fr](https://www.dcode.fr/brainfuck-language) and got

```
dep0q3
```

![idcode](./assets/dcode.png)

I tried this as the zip password, but it failed. Seeing the format of that password, it hinted at a `ROT13` cipher, SO i DECODED IT

```bash
echo dep0q3 | tr 'A-Za-z' 'N-ZA-Mn-za-m'                           
```

![rot13](./assets/rot13.png)

Using `qrc0d3` as the password, I successfully extracted the zip

```bash 
unzip qr_flag.zip  
```

![unzip](./assets/unzip.png)

Finally, I read the flag

```bash
cat flag.txt                            
```

![flag](./assets/flag.png)

This challenge was a clever mix of steganography and cipher 

---

```
FLAG : flag{qr_c0des_ar3_fun}
```