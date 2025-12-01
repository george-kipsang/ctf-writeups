## Description

Hey, it's me - Stewie Griffin. I heard the t3rmin4tors are hosting a Flash CTF in Nairobi, and naturally, I had to show up to dominate. But things went totally sideways during the maandamano. Long story short: chaos, tear gas and somehow I landed in a place I don't even remember! My friend left me a clue about where I was before I got lost - it's hidden somewhere inside this image I snapped before everything went dark. Can you help me figure out where I was before I vanished? All I want is to make it to the CTF on time. Find out where the clue is!

---
## Solution

First, I opened the image to check what it showed. It was a picture of me having a picnic in the park. In the background, I noticed a small lake and a building that looked like the parliament. That seemed like a good starting point.

Next, I ran `exiftool` on the image to inspect its metadata

```bash 
exiftool picnic.png            
```

![exiftool](./assets/exiftool.png)

The output revealed a lot of standard info, but one thing caught my eye — the **Description** field contained this string ``666c61677b315f7734355f683372337d`

At first, I thought it might be a hash, but it didn’t match common hash formats like MD5 or SHA256. Instead, it looked like a hexadecimal-encoded ASCII string.

To decode it, I used the `xxd` command and it gave me the flag

```bash
echo "666c61677b315f7734355f683372337d" | xxd -r -p
```

![xxd](./assets/xxd.png)

 - The command `xxd -r -p` converts a plain hex string back into readable text or binary.

   - `-r` means reverse the hex dump back to original data.
   - `-p` means the input is a continuous hex string without extra formatting.

So the clue was right there in the metadata — a hex-encoded flag telling me exactly where I was before I vanished.

---

```
FLAG : flag{1_w45_h3r3}
```
