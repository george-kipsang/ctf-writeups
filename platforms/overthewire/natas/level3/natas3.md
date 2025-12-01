###  Description

```
Username: natas3
URL:      http://natas3.natas.labs.overthewire.org
```

### Walkthrough

Accessed the webpage

![webpage](./assets/webpage.png)

Viewed the source code

![source-code](./assets/source-code.png)

Since there was nothing, I decided to visit robots.txt

Robots.txt is a file in which the directories plus files that you don't want to be indexed by google web crawlers you place

![robots](./assets/robots.png)

In it, was a directory `s3cr3t`

![s3cr3t](./assets/s3cr3t.png)

The contents had `users.txt` file, thus we get it

```bash
wget --user=natas3 --password=3gqisGdR0pjm6tpkDKdIWO2hSvchLeYH http://natas3.natas.labs.overthewire.org/s3cr3t/users.txt
```
![wget](./assets/wget.png)

Opened the file and got the creds for natas4

![user4](./assets/user4.png)

```
password4 : QryZXc2e0zahULdHrtHxzyYkj59kUxLQ
```
