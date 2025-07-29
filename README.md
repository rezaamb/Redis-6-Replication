if you want change setting or uncluster 6 nodes use these commands : 


1. Stop Redis:
```bash
systemctl stop redis
```

2. Delete Cluster Files And Data:
```bash
rm -f /var/lib/redis/nodes-*.conf
rm -f /var/lib/redis/dump.rdb
rm -f /var/lib/redis/appendonly.aof
```

3. For Cluster Reset Hard For Each Node :
```bash
redis-cli -h 172.x.x.x -p <Port> -a "<StrongPassword>" FLUSHALL
redis-cli -h 172.x.x.x -p <Port> -a "<StrongPassword>" CLUSTER RESET HARD
```
Check Node Status :

```bash
redis-cli -h 172.x.x.x -p <Port> -a "<StrongPassword>" CLUSTER NODES
```
4. Start Redis Again :
```bash
sudo systemctl start redis
```

5. Make Cluster Again :
```bash
redis-cli --cluster create \
172.16.1.1:<Port> \
172.16.1.2:<Port>\
172.16.1.3:<Port>\
172.16.1.4:<Port> \
172.16.1.5:<Port> \
172.16.1.6:<Port>\
--cluster-replicas 1 \
-a "<StrongPassword>"
```











