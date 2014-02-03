# Release Notes

## Version 2.3.0 (February 3rd 2014)
+ Support for [Redis Cluster](http://redis.io/topics/cluster-spec)
+ Implement [SCAN command](http://redis.io/commands/scan)                                                                                                                                                                                                            
+ Implement [PUBSUB command](http://redis.io/commands/pubsub)
+ Upgrade to [Apache Commons Pool 2](http://commons.apache.org/proper/commons-pool/)
+ And lots of small bugfixes and improvements
 
## Version 2.1.0 (May 10th 2012)
+ Support for Scripting
+ Support for Variadic commands
+ Support for new Object commands
+ Support for Slowlog commands
+ Lots and lots of bug fixes.

## Version 2.0.0 (May 30th 2011)
+ remove watch from transaction (Jonathan Leibiusky)
+ Add binary responses to binary transaction (Ingvar Bogdahn)
+ binary jedis watch command accepts several keys (Jonathan Leibiusky)
+ On reconnection, select the correct db index (Jonathan Leibiusky)
+ hvals response type consistency, was Collection should have been List (Jonathan Leibiusky)
+ MasterSlave consistency and old mode compatibility with shard names (Dario Guzik)
+ list command receive now long parameters to be consistent with return type (Jonathan Leibiusky)
+ add Tuple compareTo (Jonathan Leibiusky)
+ add getDB() which return the db number we are connected to (Jonathan Leibiusky)
+ add publish command to Pipeline (Jonathan Leibiusky)
+ add publish to transaction (Jonathan Leibiusky)
+ fix md5 hashing as MessageDigest is not threadsafe, now using ThreadLocal (Jonathan Leibiusky)
+ throw JedisDataException when sending NULL values to redis as it is not a valid value in the protocol (Jonathan Leibiusky)
+ throw a JedisConnectionException if unsubscribing from a not subscribed JedisPubSub instance (Jonathan Leibiusky)
+ handle quit command response as it was leaving the socket in an unconsistent state (Jonathan Leibiusky)
+ jedis monitor should set client socket timeout to infinite (Jonathan Leibiusky)
+ adding bit commands to ShardedJedisPipeline (ewhauser)
+ add del() to ShardedJedis (Jonathan Leibiusky)
+ don't check if jedis is connected as PING will connect if necessary (Jonathan Leibiusky)
+ fix reversed boolean logic for setbit (Eric Hauser)
+ multi/exec block return formatted responses (Jonathan Leibiusky)
+ pipeline return formatted values (Jonathan Leibiusky)
+ made shard iterable in the same order as in config during creation (Dmytro)
+ fix issue 108, brpoplpush set infinite timeout before waiting for a reply from the server (Jonathan Leibiusky)
+ should always default to Murmur to be consistent everywhere (Jonathan Leibiusky)
+ ISSUE 78: Removed logic that waits forever till all shards are connected in ShardedJedisPool Ensuered that all commands connect at the beginning if necessary. (Dmytro)
+ Compare Tuples based on the element, not the element & score (Jon Bracy)
+ Implementation of ZREVRANGEBYSCORE command (lmar)
+ Generate shard nodes by shard position. Shards are now host/port independent (Dario Guzik)
+ Generate keys for different shards (Dario Guzik)
+ add public setrange and getrange (Jonathan Leibiusky)
+ pubsub should flush commands ASAP since it won't read from the socket (Jonathan Leibiusky)
+ adding support for bit manipulation in to pipeline (ewhauser)
+ Adding support for bit commands get/setrange (Eric Hauser)
+ Adding setbit/getbit to ShardedJedis (Eric Hauser)
+ Fixing Jedis.(get|set)bit to use use booleans as input and output to better match Redis commands (Eric Hauser)
+ Use #discard on the transaction object (Pieter Noordhuis)
+ Don't wait for QUEUED replies in MULTI (Pieter Noordhuis)
+ Use regular HashSet instead of LinkedHashSet for unordered replies (Pieter Noordhuis)
+ Flush to socket when starting to read (Pieter Noordhuis)
+ Connect.connect() now honors timeout value (Mike Hobbs)

## Version 1.5.2 (February 4th 2011) 
+ add JedisDataException and JedisConnectionException
+ add binary support for pubsub
+ pubsub command check if we are connected
+ update missing sort command in transactions api
+ add defaults to pool config
+ added JedisPoolConfig getters/setters so object pool can be configured with Spring/IoC
+ Binary client delegates for Pipline
+ add missing sort overloads to Transaction
+ setbit and getbit receive long offset in BinaryJedis
+ update transaction api with all the new commands of jedis 1.5.1
+ updates on debug command

