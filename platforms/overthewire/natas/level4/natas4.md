### Description

```
Username: natas4
URL:      http://natas4.natas.labs.overthewire.org
```

### Walkthrough

Viewed the source code and got a text saying : `Access disallowed. You are visiting from "" while authorized users should come only from "http://natas5.natas.labs.overthewire.org/"`

![webpage](./assets/webpage.png)

Upon refreshing the page, we get this : `Access disallowed. You are visiting from "http://natas4.natas.labs.overthewire.org/" while authorized users should come only from "http://natas5.natas.labs.overthewire.org/"`

![refresh](./assets/refresh.png)

As per the message, we note that we need to forward a request from the natas5 URL, so we ought to edit the Refer to natas5 url
Capture the request with burpsuite and head over to the refer part

![burp1](./assets/burp1.png)

Edit it and input the url : `http://natas5.natas.labs.overthewire.org/`

![burp2](./assets/burp2.png)

We are told access granted and we are given the password for the next level

```
password5 : 0n35PkggAPm2zbEpOU802c0x0Msn1ToK
```









