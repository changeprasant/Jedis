# Release Notes

## Version 2.0 coming soon

## Version 1.5.2 (Februar 4th 2011) 
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

