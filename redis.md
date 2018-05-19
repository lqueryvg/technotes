# Redis

```
$ docker run --name some-redis -d redis redis-server --requirepass foobared
896132042afb124aae06ea855e02da2a211bd0540a862f1d785b586a3bbc4479
$ docker run -it --link some-redis:redis --rm redis redis-cli -h redis -p 6379
redis:6379> AUTH test
(error) ERR invalid password
redis:6379> AUTH foobared
OK
redis:6379> 
```
