## Description

A strange encrypted message was discovered in an old backup, buried under layers of weak but cleverly stacked ciphers. It looks simple—maybe too simple. Can you break through the layers and uncover the flag?

---

## Challenge

I opened the contents of the file

```bash
cat crypto_challenge1.txt
```

![crypto](./assets/crypto.png)

---

## Solution

We are provided with a file containing some descriptions and an encrypted message:  
`c3ludHtmMzNfMWdmX2d1NGdfMzRmbH0=`

The presence of the trailing `=` suggests the message is Base64 encoded. To decode it, we can use online tools like [CyberChef](https://gchq.github.io/CyberChef/) or [dcode.fr](https://www.dcode.fr/base-64-encoding), but I prefer using the terminal:


```bash
echo "c3ludHtmMzNfMWdmX2d1NGdfMzRmbH0=" | base64 -d  
```

![base64](./assets/base64.png)

The decoded string looks like a flag but appears to be encoded with another cipher. Recognizing the pattern, it is likely `ROT13`, which shifts each alphabet letter 13 places forward, wrapping around the alphabet, while numbers and symbols remain unchanged. This selective letter substitution is a hallmark of `ROT13` encoding.

Applying `ROT13` using `CyberChef` or `dcode.fr` reveals the actual flag.

Alternatively, you can decode `ROT13` in the terminal using the `tr` command
`tr is used to translate or delete characters`

```bash
echo "synt{f33_1gf_gu4g_34fl}  " | tr 'A-Za-z' 'N-ZA-Mn-za-m' 
```

![rot13](./assets/rot13.png)

---

```
FLAG : flag{s33_1ts_th4t_34sy} 
```