# Telegram Dante server for Docker

> Based on original work by [schors](https://github.com/schors/tgdante).

## Features against original tgdante

* deployable to remote machine with docker-machine;
* important files are stored in named volume;
* requires no mounts to host file system.

> ## Please, note
> 
> * **Mounts:** `/etc` is mounted to volume completely as Docker doesn't support particular file mounts to named volumes. 
> * **Encryption:** by default credentials are sent __unencrypted.__ You can inspect traffic with Wireshark (look for PUSH/ACK packages) to find out which data is transmitted in plain text. This is due SOCKS5 is a data transport protocol, not encryption strategy.
> * **Block all irrelevant ports:** to prevent Dante server recon by third-party bots and researchers, bind all ports except SOCKS5 to static IP/VPN. This can prevent server recon or misconfigure flaws.
> * **No license, no warranty:** it's just a PoC with dozens of omited obvious tips. Use it at you own risk.

## Usage

**Requirements:** Docker 18.01, docker-compose 1.18+. Should also work on previous versions.

### Configure server

| Location              | Param            | Description                  |
|:----------------------|:-----------------|:-----------------------------|
| etc/sockd.conf:9      | `external: eth0` | Host interface to bind       |
| etc/sockd.conf:6      | `...port = 1080` | Port to bind                 |
| docker-compose.yml:14 | `WORKERS=20`     | Max amount for dante workers |

Since config uses host network, port exposing or mapping is obsolete.

### Run server

Start Dante in detached mode. Image will be build in-place:

```
➜ docker-compose up -d
```

Check Dante is running:

```
➜ docker ps -a
CONTAINER ID        STATUS              NAMES
01645bd5bc5a        Up 3 minutes        dante
```


### Users management

Dante config relies on system user auth. To manage users, run commands inside Dante container:

```
# get ID of dante container
➜ docker ps -a
...

# run sh inside container
➜ docker exec -it <ID> bash
root@dante:/#
```

#### Add user

Danter config relies on system user auth. To add Dante user add a new system one:

```
# specify credentials for new user
USER_NAME=user0
PASSWORD=password123

# add user and set password
useradd "$USER_NAME"
echo "${USER_NAME}:${PASSWORD}" | chpasswd -c SHA256
```

Don't forget to specify a new user can't login via SSH, has no shell, etc (not covered in this doc).

To change password or delete user refer to common Linux/Ubuntu commands.
