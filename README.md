# redis-cluster-with-sentinel
**Redis cluster with Docker Compose** 

There are following services in the cluster,

* master: Master Redis Server
* slave1:  Slave Redis Server
* slave2:  Slave Redis Server
* sentinel: Sentinel Server


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
**At this time, we can test whether the synchronization is successful. Open three windows to log in to redis, redis cli-p 6379, redis cli-p 6380, redis cli-p 6381

**set 123 123 in the main library 6379, and then get 123 in the slave Library
Check the status of redis cluster
```
docker-compose ps
```
The result is 
```
               Name                              Command               State    Ports   
---------------------------------------------------------------------------------------
redisclusterwithsentinel_master_1     docker-entrypoint.sh redis ...   Up      6379/tcp 
redisclusterwithsentinel_sentinel_1   entrypoint.sh                    Up      6379/tcp 
redisclusterwithsentinel_slave_1      docker-entrypoint.sh redis ...   Up      6379/tcp 
```
**No problem. Now let's test whether the sentinel mode works. If the main database is down for some reason, can the slave database automatically switch roles
**We can manually stop the container process of the main database to simulate downtime
```
docker stop redissentinel_master_1
```

Scale out the instance number of sentinel

```
docker-compose scale sentinel=3
```

Scale out the instance number of slaves

```
docker-compose scale slave=4
```

Check the status of redis cluster

```
docker-compose ps
```

The result is 

```
               Name                              Command               State    Ports   
---------------------------------------------------------------------------------------
redisclusterwithsentinel_master_1     docker-entrypoint.sh redis ...   Up      6379/tcp 
redisclusterwithsentinel_sentinel_1   entrypoint.sh                    Up      6379/tcp 
redisclusterwithsentinel_sentinel_2   entrypoint.sh                    Up      6379/tcp 
redisclusterwithsentinel_sentinel_3   entrypoint.sh                    Up      6379/tcp 
redisclusterwithsentinel_slave_1      docker-entrypoint.sh redis ...   Up      6379/tcp 
redisclusterwithsentinel_slave_2      docker-entrypoint.sh redis ...   Up      6379/tcp 
redisclusterwithsentinel_slave_3      docker-entrypoint.sh redis ...   Up      6379/tcp 
redisclusterwithsentinel_slave_4      docker-entrypoint.sh redis ...   Up      6379/tcp 
```

For stop master redis server.
```
 docker-compose unpause master
```
And get the sentinel infomation with following commands

```
docker-compose exec sentinel redis-cli -p 26379 SENTINEL get-master-addr-by-name mymaster
```

## References

[https://github.com/mustafaileri/redis-cluster-with-sentinel][1]

[https://programmer.help/blogs/building-redis-highly-available-cluster-sentinel-mode-based-on-docker-compose.html][2]



