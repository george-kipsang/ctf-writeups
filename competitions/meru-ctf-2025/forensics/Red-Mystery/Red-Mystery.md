## Description
"Sometimes the most colourful thoughts are worth writing down. When the clouds speak in riddles, perhaps it's worth turning their words into keys." The answer might be hiding in what's said, not just what's seen. Something about this photo doesn't feel natural. Can you discover its hidden secret?

---
## Hint

Ever heard of a wordlist ...!!!

---
## Solution

When I first opened the image, I saw a tarmac road bordered by trees, with what appeared to be a sunset in the background. Although the scene looked ordinary at first glance, the challenge description made me suspect there was more beneath the surface.

My initial approach was to examine the image’s metadata using `exiftool`. This tool is excellent for uncovering hidden information embedded in files. Running the command:

```bash
exiftool challenge.jpg  
```

![exiftool](./assets/exiftool.png)

I carefully reviewed the output and noticed an intriguing comment
`Aint sth good like when the cloud is purple`

This comment stood out as it seemed deliberately placed and possibly relevant to the challenge.

Suspecting there might be hidden data, I next tried extracting information using `steghide`

```bash
steghide extract -sf challenge.jpg 
```

![steghide](./assets/steghide.png)

However, `steghide` prompted me for a passphrase, which I didn't have. At this point, I decided to use a brute-force approach with `stegseek`, leveraging the popular `rockyou.txt` wordlist

```bash
stegseek challenge.jpg /usr/share/wordlists/rockyou.txt
```

![stegseek](./assets/stegseek.png)

After some time, `stegseek` successfully found the passphrase "**purplecloud**"

It also revealed that the hidden file was named `flagged.xcf` and extracted it as `challenge.jpg.out`. Since `.xcf` is the native file format for GIMP, I opened this file in GIMP to investigate further. Upon opening it, I found the flag embedded within the image layers.

Alternatively, for a quicker approach, I used the `strings` command to search for flag-like patterns directly in the extracted file:

```bash
strings challenge.jpg.out | grep flag{
```

![flag](./assets/flag.png)

---

```
FLAG : flag{r3ds_are_susp1c10us}
```
