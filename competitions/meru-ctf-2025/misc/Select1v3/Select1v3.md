## Description

Somewhere lies the flag...!!!

---
## Solution

I was handed a zip file  and upon extracting it, I found a maze of folders and even another zip file nested inside. It was a zip bomb

```bash
unzip partial.zip      
```

![unzip](./assets/unzip1.png)

Faced with this sprawling extraction, my first instinct was to clean house—delete all the extracted clutter so I could start fresh. But I needed to be careful not to delete the original zip file or any other important files.

![ls](./assets/ls.png)

To do this, I identified the files I wanted to keep—my `partial.zip` and a stray image file called `pasted image.png`. Then, I ran this command to wipe everything else

```bash
find . -maxdepth 1 ! -name '*.zip' ! -name '.' -exec rm -r {} + 
```

![find](./assets/find.png)

What the command does:

- `find .`: Search in the current directory (`.`).

- `-maxdepth 1`: Only look at files/directories directly inside the current directory (do not search recursively).

- `! -name '*.zip'`: Exclude files with the `.zip` extension.

- `! -name '.'`: Exclude the current directory itself from the results.

- `rm -r` recursively deletes directories and their contents.

- The `+` at the end groups multiple files/directories for efficiency.

- This will delete **all files and directories** except `.zip` files.

![deletion](./assets/deletion.png)
Indeed, every folder was deleted. I accessed the nested_decoy file but it had nothing

To view the content of the zip file next without extracting it, I used  `-l` flag. This showed file names, sizes, and timestamps

```bash
 unzip -l partial.zip                                  
```

![secrets](./assets/secrets.png)

Before even I proceeded further, one line caught my eye, a hint I had missed before : `Extract with care. Real base64 flag shards are in home/secrets/flag_*.txt` 

The first thing I thought was to see if indeed there was existence of such a file with the file path  `/home/secret`, and indeed it was there, e.g

![unzip2](./assets/unzip2.png)

Next, I now extracted only the files with those file path and outputted in a folder called `extracted`

```bash
unzip partial.zip "home/secrets/flag_*.txt" -d extracted  
```

![unzip3](./assets/unzip3.png/)

Inside the `extracted/home/secrets` folder, I found four files named `flag_1.txt` through `flag_4.txt`. Their contents looked like fragments of a puzzle

```bash
cat extracted/home/secrets/flag_*.txt | base64 -d
```

![extracts](./assets/extracts.png)

Recognizing these as `Base64-encoded strings`, I combined and decoded them in one go


```bash
cat extracted/home/secrets/flag_*.txt | base64 -d
```

![decode](./assets/decode.png)

And just like that, the flag revealed itself.

---

```
FLAG : flag{3xtr4ct_0nly_wh4t_m4tt3rs} 
```
