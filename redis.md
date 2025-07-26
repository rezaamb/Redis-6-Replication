## How To Install and Secure Redis on Ubuntu 20.04

Step 1 — Installing and Configuring Redis :
```bash
sudo apt update
```

Then install Redis by typing:
```bash
sudo apt install redis-server
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
then get status Redis :
```bash
systemcl status redis
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
