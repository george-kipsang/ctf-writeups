### Description

```
Username: natas5
URL:      http://natas5.natas.labs.overthewire.org
```

### Walkthrough

Viewing the webpage reveals to us that access disallowed and we are not logged in.

![webpage](./assets/webpage.png)

Whenever I see anything concerning authentication, the first thing that coms to my mind is cookies. I thus decide to inspect the webpage for cookies

![cookie0](./assets/cookie0.png)

We find a cookie named `loggedin` and its value is 0. I changed it to 1

![cookie1](./assets/cookie1.png)

Changing it to 1,  we are now logged in and we have the password

![password](./assets/password.png)

```
password : 0RoJwHdSKWFTYR5WuiAewauSuNaBXned
```

