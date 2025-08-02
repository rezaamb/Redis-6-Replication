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
then give permission to ```/var/lib/redis```
```bash
sudo chown -R redis:redis /var/lib/redis
sudo chmod 750 /var/lib/redis
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
or 
```

bind 172.x.x.x 127.0.0.1
requirepass <strongpassword>
masterauth <strongpassword>
cluster-enabled yes
cluster-config-file nodes.conf
cluster-node-timeout 5000
appendonly yes
port 6379
protected-mode yes
tcp-backlog 511
timeout 0
tcp-keepalive 300
daemonize yes
supervised auto
pidfile /run/redis/redis-server.pid
loglevel notice
logfile /var/log/redis/redis-server.log
databases 16
always-show-logo no
set-proc-title yes
proc-title-template "{title} {listen-addr} {server-mode}"
stop-writes-on-bgsave-error yes
rdbcompression yes
rdbchecksum yes
dbfilename dump.rdb
rdb-del-sync-files no
dir /var/lib/redis
replica-serve-stale-data yes
replica-read-only yes
repl-diskless-sync yes
repl-diskless-sync-delay 5
repl-diskless-sync-max-replicas 0
repl-diskless-load disabled
repl-disable-tcp-nodelay no
replica-priority 100
acllog-max-len 128
lazyfree-lazy-eviction no
lazyfree-lazy-expire no
lazyfree-lazy-server-del no
replica-lazy-flush no
lazyfree-lazy-user-del no
lazyfree-lazy-user-flush no
oom-score-adj no
oom-score-adj-values 0 200 800
disable-thp yes
#appendonly no
appendfilename "appendonly.aof"
appenddirname "appendonlydir"
appendfsync everysec
no-appendfsync-on-rewrite no
auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb
aof-load-truncated yes
aof-use-rdb-preamble yes
aof-timestamp-enabled no
slowlog-log-slower-than 10000
slowlog-max-len 128
latency-monitor-threshold 0
notify-keyspace-events ""
hash-max-listpack-entries 512
hash-max-listpack-value 64
list-max-listpack-size -2
list-compress-depth 0
set-max-intset-entries 512
zset-max-listpack-entries 128
zset-max-listpack-value 64
hll-sparse-max-bytes 3000
stream-node-max-bytes 4096
stream-node-max-entries 100
activerehashing yes
client-output-buffer-limit normal 0 0 0
client-output-buffer-limit replica 256mb 64mb 60
client-output-buffer-limit pubsub 32mb 8mb 60
hz 10
dynamic-hz yes
aof-rewrite-incremental-fsync yes
rdb-save-incremental-fsync yes
jemalloc-bg-thread yes
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
systemctl restart redis
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

some commands for listenning and testing :

```
ufw status
sudo ufw disable

ss -tuln | grep <port>

ls -ltrh /var/lib/redis/ 
```

Here, you can see that Redis is running and is already enabled, meaning that it is set to start up every time the server boots.

To test that Redis is functioning correctly, connect to the server using redis-cli, Redis’s command-line client:
```bash
redis-cli -p <port>
```
```
auth <strongPasswd>
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
for see clusters : 

```bash
cluster nodes
```

some commands for listenning and testing :

```
ufw status
ss -tuln | grep 6379
ls /var/lib/redis/ -alh
```



---------------------------------------------------------------------------------------------------------------------------------



For making user :

go to ```vim /etc/redis/redis.conf``` and add this command :

```bash
USER <user> on ><Password>  ~<user>:* +@all
```
and then Restart Redis :

```bash
systemctl restart redis
```

for using that : 
```bash
redis-cli -u redis://<user>:<Password>@172.16.1.1:<Port>
```
```bash
ACL LIST
ACL USERS
ACL GETUSER <username>
```
