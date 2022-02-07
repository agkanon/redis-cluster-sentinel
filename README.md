# redis-cluster-with-sentinel
**Redis cluster with Docker Compose** 

There are following services in the cluster,

* master: Master Redis Server
* slave1:  Slave Redis Server
* slave2:  Slave Redis Server
* sentinel1: Sentinel Server
* sentinel2: Sentinel Server

The sentinels are configured with a "mymaster" instance with the following properties -

```
sentinel monitor mymaster redis-master 6379 2
sentinel down-after-milliseconds mymaster 5000
sentinel parallel-syncs mymaster 1
sentinel failover-timeout mymaster 5000
```

The details could be found in sentinel/sentinel.conf

The default values of the environment variables for Sentinel are as following

* SENTINEL_QUORUM: 2
* SENTINEL_DOWN_AFTER: 5000
* SENTINEL_FAILOVER: 5000



## Try it

Build and start services
```
docker-compose up --build
```
Or
```
docker-compose up --force-recreate
```
If you want to run in the background, you can add the - d parameter

**At this time, we can test whether the synchronization is successful. Open three windows to log in to redis, redis-cli -p 6379, redis-cli -p 6380, redis-cli -p 6381**

**set 123 123 in the main library 6379, and then get 123 in the slave Library**
Check the status of redis cluster
```
docker ps
```
The result is 
```
               Name                              Command               State    Ports   
---------------------------------------------------------------------------------------
redis-cluster-sentinel_sentinel2   "sentinel-entrypoint…"               UP      6379/tcp, 26379/tcp                      
redis-cluster-sentinel_sentinel1   "sentinel-entrypoint…"               UP      6379/tcp, 26379/tcp                      
redis-cluster-sentinel_slave2_1    "docker-entrypoint.s…"               UP      0.0.0.0:6381->6379/tcp, :::6381->6379/tcp   
redis-cluster-sentinel_slave1_1    "docker-entrypoint.s…"               UP      0.0.0.0:6380->6379/tcp, :::6380->6379/tcp   
redis-cluster-sentinel_master_1    "docker-entrypoint.s…"               UP      0.0.0.0:6379->6379/tcp, :::6379->6379/tcp   
```
**No problem. Now let's test whether the sentinel mode works. If the main database is down for some reason, can the slave database automatically switch roles**

**We can manually stop the container process of the main database to simulate downtime**
```
docker stop redissentinel_master_1
```
**At this time, the master database is no longer connected. We enter the slave database and use the info command to view the role of the slave database**

## References

[https://programmer.help/blogs/building-redis-highly-available-cluster-sentinel-mode-based-on-docker-compose.html][1]

[1]: https://programmer.help/blogs/building-redis-highly-available-cluster-sentinel-mode-based-on-docker-compose.html


