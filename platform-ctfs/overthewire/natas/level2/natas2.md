### Description

```
Username: natas2
URL:      http://natas2.natas.labs.overthewire.org
```

### Walkthrough

Accessed the webpage

![webpage](./assets/Webpage.png)

Tried source code

![source-code](./assets/Source-code.png)

There was nothing there, but I got an image link. 

![files-url](./assets/Files-url.png)


Upon visiting it, there was nothing but the url seemed interesting
I navigated back to the files directory and I saw a file, users.txt

![files](./assets/Files.png)

I used `wget` command to get the file and I viewed the contents of users.txt. For it, I got a 401 error message meaning I needed to be authenticated

```bash
wget --user=natas2 --password=TguMNxKo1DSa1tujBLuZJnDUlCcUAPlI http://natas2.natas.labs.overthewire.org/files/users.txt
```

![wget](./assets/wget.png)

Contents of users.txt

![users](./assets/Users.png)

```
password3 : 3gqisGdR0pjm6tpkDKdIWO2hSvchLeYH
```


