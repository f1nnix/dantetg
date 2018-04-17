# Telegram Dante server for Docker

> Based on original repo by [schors](https://github.com/schors/tgdante).

## Features against original tgdante

* deployable to remote machine with docker-machine;
* important files are stored in named volume;
* requires no mounts to host file system.

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

Danter config relies on system user auth. To manage users, run commands inside Dante container:

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

To change password or delete user refer to common Linux/Ubuntu commands.
