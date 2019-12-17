# dynomite-redis-docker-demo

Two Redis instances and two Dynomite instances. Each Dynomite instance acts as a sidecar to its corresponding Redis. Redis clients connect to one of the Dynomite instances, and their commands are replicated across the Dynomite instances, meaning that adding a key in one Dynomite will result in the key being written to both Redis instances.


## Quick start

### 1. Bring up the containers:

```
docker-compose up -d
```

You should now have four containers, a network and two volumes.

### 2. Add a key via Dynomite

Add a new key via `dynomite-1`:

```
docker exec -it dynomite-redis-demo_redis-1_1 redis-cli -h dynomite-1 -p 8102 set hello world
```

You should get an `OK` response.

### Check Redis instances for the key

Now we can check whether the key is available in both of the Redis instances.

Check that key is in `redis-1`:

```
docker exec -it dynomite-redis-demo_redis-1_1 redis-cli -h redis-1 get hello
```

Should get a `"world"` response.

Do the same for `redis-2`:

```
docker exec -it dynomite-redis-demo_redis-1_1 redis-cli -h redis-2 get hello
```

Should also get a `"world"` response. This means that by adding the key via `dynomite-1` it was stored on both `redis-1` and `redis-2`.

### Delete the key

Delete key `hello` via `dynomite-2`:

```
docker exec -it dynomite-redis-demo_redis-1_1 redis-cli -h dynomite-2 -p 8102 del hello
```

Should get a `(integer) 1` response.

### Check Redis instances for the key

The key should be delete from both instances.

Check `redis-1` for the key `hello`:

```
docker exec -it dynomite-redis-demo_redis-1_1 redis-cli -h redis-1 get hello
```

Should get a `(nil)` response.

```
docker exec -it dynomite-redis-demo_redis-1_1 redis-cli -h redis-2 get hello
```

Should also get a `(nil)` response.