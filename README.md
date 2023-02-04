# Tools
In this document i'll explain tools used for completing CTFs.

## Basic Usage
Basic tools and commands that can be used in CTFs, but not only for this.

### SSH
Basic command, remote conection to a server, remote login and command-line exectuion. Can use an anonymous login, but usually needs a login.

```shell
ssh user@192.168.0.0
```

As an additional information, we can obtain the publis RSA key in a hidden directory in the /home directory of every user, named **/.ssh**.
Public RSA keys are fundamental for remote connections. Once we have a RSA key, we can save it into a file, and use it to connect via SSH.

```shell
ssh -i publicKeyFile user@192.168.0.1
```

If its asked a passphrase while using the RSA key, we can decode it using **ssh2john**. It returns a hash, that can be decoded using **JohnTheRipper**.
```shell
ssh2john publicKeyFile > outputHash.txt

john outputHash.txt --wordlist=/path/to/wordlist/
```

### SCP
Related to SSH, transfering files from our machine to the remote server using the SSH protocol.

```shell
scp file.txt user@192.168.0.0:/home
```

## Recon

### NMAP
Most used port scanning tool. 

Basic Scan:
```shell
nmap -sC -sV -oN file.txt 192.168.0.0

    -sC: Using defualt script
    -sV: Checking for services / version in every port
    -oN: Saving the output in the file
```

Aggresive scan, scanning every single TCP port, without ping or dns:
```shell
nmap -n -Pn -sS 192.168.0.0 --open -sV

    -n: Without DNS resolution
    -Pn: Skipping host discovery
    -sS: TCP SYN ports
    --open: Only shows open or possibly open ports
```

### Dirb
Website directories scan, using a wordlist. Other similar tools are dirbuster or gobuster.
```shell
dirb http://192.168.0.0:80 /path/to/wordlist -X .txt,.html,.php
```
We need to know that sometimes, there will be **.ttx, .html, .php, etc.** sites to be found.

### Enum4linux
Attempts to get the userlist and OS information, among other usefull information from a target.
```shell
enum4linux -U -o 192.168.0.0

    -U: userlist
    -o: OS info
```

## Exploitation

### Hydra
Login bruteforces, for different services, such as SSH, FTP, etc.
```shell
hydra -l user -P /path/to/wordlist/ ssh://192.168.0.0

    -l: a specified user
    -L: a wordlist of users
    -p: a specified password
    -P: a wordlist of passwords
    -ssh: can be changed for any other service
```

### LinePEAS
Once we are connected to the remote host, we can use the **linpeas.sh** script, which gave us possible vulnerabilities in the system, such as exposed SSH keys, or running services.

### Docker
If the user is privileged enough to run docker, it can be used to break out from restricted environments by spawning an interactive root shell.
More information can be seen in [GTFOBins](https://gtfobins.github.io/gtfobins/docker/).

```shell
docker run -v /:/mnt --rm -it alpine chroot /mnt sh
```
