## How To Install and Secure Redis on Ubuntu 20.04

Step 1 — Installing and Configuring Redis :
```bash
sudo apt update

apt-get install lsb-release curl gpg

curl -fsSL https://packages.redis.io/gpg | sudo gpg --dearmor -o /usr/share/keyrings/redis-archive-keyring.gpg

sudo chmod 644 /usr/share/keyrings/redis-archive-keyring.gpg

echo "deb [signed-by=/usr/share/keyrings/redis-archive-keyring.gpg] https://packages.redis.io/deb jammy main" | sudo tee /etc/apt/sources.list.d/redis.list
```

Then install Redis by typing:
```bash
apt update
apt install redis-tools
sudo apt install redis-server -y
or
snap install redis
```

Open this file with your preferred text editor:
```bash
sudo vim /etc/redis/redis.conf
```
```
bind 172.16.5.1 127.0.0.1
requirepass <strongpassword>
masterauth <strongpassword>
cluster-enabled yes
cluster-config-file nodes.conf
cluster-node-timeout 5000
appendonly yes
port 6379
```

for get a strong password :
```bash
openssl rand 60 | openssl base64 -A
```
```
Output
RBOJ9cCNoGCKhlEBwQLHri1g+atWgn4Xn4HwNUbtzoVxAYxkiYBi7aufl4MILv1nxBqR4L6NNzI0X6cE
```
Step 2 — Testing Redis :


then get status Redis :
```bash
systemctl status redis
```
If it is running without any errors, this command will produce output similar to the following:

```
Output
● redis-server.service - Advanced key-value store
     Loaded: loaded (/lib/systemd/system/redis-server.service; enabled; vendor preset: enabled)
     Active: active (running) since Thu 2020-04-30 23:26:54 UTC; 4s ago
       Docs: http://redis.io/documentation,
             man:redis-server(1)
    Process: 36552 ExecStart=/usr/bin/redis-server /etc/redis/redis.conf (code=exited, status=0/SUCCESS)
   Main PID: 36561 (redis-server)
      Tasks: 4 (limit: 2345)
     Memory: 1.8M
     CGroup: /system.slice/redis-server.service
             └─36561 /usr/bin/redis-server 127.0.0.1:6379
. . .
```
Here, you can see that Redis is running and is already enabled, meaning that it is set to start up every time the server boots.

To test that Redis is functioning correctly, connect to the server using redis-cli, Redis’s command-line client:
```bash
redis-cli
```
In the prompt that follows, test connectivity with the ping command:
```
ping
```
```
Output
PONG
```
This output confirms that the server connection is still alive. Next, check that you’re able to set keys by running:
```
set test "It's working!"
```
```
Output
OK
```
Retrieve the value by typing:

```
get test
```
Assuming everything is working, you will be able to retrieve the value you stored:

```
Output
"It's working!"
```

do it for all nodes , then run this command for replication :
```bash
redis-cli --cluster create \
172.16.5.1:6379 \
172.16.5.2:6379 \
172.16.5.3:6379 \
172.16.5.4:6379 \
172.16.5.5:6379 \
172.16.5.6:6379 \
--cluster-replicas 1 \
-a "<strongpassword>"
```

Then :

```bash
systemctl daemon-reload
systemctl restart redis
```
for test : 

```bash
redis-cli -c -p 6379
```
and Then for Authentication give it your Password :

```bash
auth <strongpassword>
```
somw commands for listenning and testing :

```
ufw status
ss -tuln | grep 6379
ls /var/lib/redis/ -alh
```
