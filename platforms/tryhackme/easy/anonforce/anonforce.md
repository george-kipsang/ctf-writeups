## Synopsis

The Anonforce TryHackMe room features a critically misconfigured vsftpd FTP server with anonymous access enabled and `anon_root=/`, exposing the entire Linux root filesystem. This vulnerability allowed me to unauthenticated enumerate sensitive files, crack a GPG key, extract `/etc/shadow`, compromise the root password, and gain full system access via SSH.

## Reconnaissance

I began by running an Nmap scan to identify open ports and services:

```bash
nmap -sC -sV 10.49.161.36
```

This revealed two open ports: **21/FTP** and **22/SSH**.

![nmap](./assets/nmap.png)

## Enumeration

I targeted the FTP service on port 21 with `anonymous` credentials. The login succeeded without a password, dropping me directly into the root directory `/` (no chroot jail). This severe misconfiguration violated vsftpd security defaults

![ftp](./assets/ftp.png)

I navigated to `/home` and identified user **melodias**

![melodias](./assets/melodias.png)

Within the user meliodas was the user flag

![user](./assets/user.png)

I downloaded and verified the user flag contents.  

![user-flag](./assets/user-flag.png)

I probed `/etc/shadow` (inaccessible directly) and SSH keys (none found). A non-standard, world-writable directory **`notread`** caught my attention.

![notread](./assets/notread.png)
## Exploitation

There are two files : `backup.pgp` and `private.asc`
Doing a little research I found what the two files might be. `private.asc` is a text file containing a private encryption key used to securely unlock and access encrypted data. `backup.pgp` is an encrypted backup file that stores protected data or documents, which can only be accessed and decrypted with the correct private key or passphrase

To download those files to your local device, use

```bash
mget *
```

In this engagement, it appears that we need to use the private key `private.asc` to decrypt `backup.pgp`. To achieve this, we need to import the key then decrypt the file

So, I imported the file using

```bash
gpg --import private.asc
```

However, it prompted me for a passphrase that I do not know

![import1](./assets/import1.png)

It was there that I resorted to bruteforce the passphrase on `private.asc` using john the ripper, since gpg keys use extractable hashes

I first converted the key into a format john the ripper can understand 

```bash
gpg2john private.asc > gpg.hash
```

![gpg2john](./assets/gpg2john.png)

I then cracked the hash using john the ripper

```bash
john -w=/usr/share/wordlists/rockyou.txt gpg.hash
```

![john1](./assets/john1.png)

I found the passphrase as `xbox360`

So, I now imported the file once more and inserted the passphrase

```bash
gpg --import private.asc
```

![import2](./assets/import2.png)

The key was successfully imported. The final step now was to decrypt the encrypted file using the imported key

```bash
gpg --output decrypted.file --decrypt backup.pgp
```

![decrypt](./assets/decrypt.png)

It accepted and I viewed the contents of the `decrypted.file`. It seemed to be the contents of the `/etc/shadow` file

In it, was the root hash which I copied at and attempted to crack it with john the ripper once more

![hash](./assets/hash.png)

```txt
$6$07nYFaYf$F4VMaegmz7dKjsTukBLh6cP01iMmL7CiQDt1ycIm6a.bsOIBp0DwXVb9XI2EtULXJzBtaMZMNd2tV4uob5RVM0
```

I copied the root hash into a file and cracked it

```bash
john -w=/usr/share/wordlists/rockyou.txt root.hash
```

![john2](./assets/john2.png)

I got the root hash which is `hikari`


## Privilege Escalation

I established SSH connection as root:

```bash
ssh root@10.49.132.128
# Password: hikari
```

Retrieved **root.txt** flag from `/root` directory.

![root-flag](./assets/root-flag.png)

## Remediation & Hardening Recommendations

**Root Cause:** vsftpd configuration with `anon_root=/` exposing entire filesystem to anonymous users.

**Immediate Hardening Steps:**

1. **Edit `/etc/vsftpd.conf`**:

```bash
anonymous_enable=NO          # Disable anonymous access completely
# OR for limited anonymous access:
# anon_root=/var/ftp         # Isolate to secure directory
anon_upload_enable=NO        # Prevent anonymous uploads
chroot_local_user=YES        # Chroot local users to home directories
write_enable=NO              # Disable file writes unless required
```

2. **Create secure anonymous directory** (if retaining anonymous access):

```bash
mkdir -p /var/ftp/pub
chown root:root /var/ftp
chmod 755 /var/ftp
```

3. **Restart vsftpd service**:

```bash
systemctl restart vsftpd
```

4. **Verification**: Anonymous FTP login now denied.

![harden](./assets/harden.png)