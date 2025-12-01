## Description

Stay focused...!!! Can you find the needle in the static?

---

## Solution

First was to extract files from the zip file given 

```bash
unzip maze_of_lies.zip 
```

![unzip1](./assets/unzip1.png)

The archive exploded into multiple folders (`logs`, `docs`, `data`, `2022`, `misc`) filled with tons of log and text files—almost like a zip bomb. Navigating through all of them would be tedious.

To simplify, I listed the archive contents with the `-l` flag. This showed file names, sizes, and timestamps without extracting

```bash 
unzip -l maze_of_lies.zip 
```

![unzip2](./assets/unzip2.png)

 Scanning through, I spotted an unusual file. All of the files had a 4 digit length but this one had 3 digits

![144](./assets/144.png)

To print the file contents directly without extracting it, I used the `-p` flag and the output was :

```bash
unzip -p maze_of_lies.zip logs/notes/old/real_note.txt
```

![unzip3](./assets/unzip3.png)

Recognizing `base64` encoding, I decoded both strings:

```bash
 echo "bWF5YmUgeW91J3ZlIGZvdW5kIHRoZSBmbGFnLCBvciBtYXliZSBub3QuCg==" | base64 -d
```

![code1](./assets/code1.png)

```bash
echo "ZmxhZ3swbmx5XzBuZV90cnVlX2YxbGVfaW5fdGgzX21hemV9" | base64 -d
```

![code2](./assets/code2.png)

This approach saved me from extracting hundreds of files and quickly led me to the flag hidden in a small, overlooked file

---

```bash
FLAG : flag{0nly_0ne_true_f1le_in_th3_maze}
```