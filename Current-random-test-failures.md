```
in redis.clients.jedis.tests.JedisClusterTest
testAskResponse(redis.clients.jedis.tests.JedisClusterTest)  Time elapsed: 0.914 sec  <<< ERROR!
redis.clients.jedis.exceptions.JedisConnectionException: Could not get a resource from the pool
	at java.net.SocksSocketImpl.remainingMillis(SocksSocketImpl.java:112)
	at java.net.SocksSocketImpl.connect(SocksSocketImpl.java:392)
	at java.net.Socket.connect(Socket.java:579)
	at redis.clients.jedis.Connection.connect(Connection.java:146)
	at redis.clients.jedis.BinaryClient.connect(BinaryClient.java:83)
	at redis.clients.jedis.BinaryJedis.connect(BinaryJedis.java:1633)
	at redis.clients.jedis.JedisFactory.makeObject(JedisFactory.java:85)
	at org.apache.commons.pool2.impl.GenericObjectPool.create(GenericObjectPool.java:861)
	at org.apache.commons.pool2.impl.GenericObjectPool.borrowObject(GenericObjectPool.java:435)
	at org.apache.commons.pool2.impl.GenericObjectPool.borrowObject(GenericObjectPool.java:363)
	at redis.clients.util.Pool.getResource(Pool.java:48)
	at redis.clients.jedis.JedisPool.getResource(JedisPool.java:85)
	at redis.clients.jedis.JedisClusterConnectionHandler.getConnectionFromNode(JedisClusterConnectionHandler.java:31)
	at redis.clients.jedis.JedisClusterCommand.runWithRetries(JedisClusterCommand.java:135)
	at redis.clients.jedis.JedisClusterCommand.runWithRetries(JedisClusterCommand.java:147)
	at redis.clients.jedis.JedisClusterCommand.runWithRetries(JedisClusterCommand.java:131)
	at redis.clients.jedis.JedisClusterCommand.run(JedisClusterCommand.java:30)
	at redis.clients.jedis.JedisCluster.set(JedisCluster.java:46)
	at redis.clients.jedis.tests.JedisClusterTest.testAskResponse(JedisClusterTest.java:268)
```

```
in redis.clients.jedis.tests.JedisClusterTest
testMigrate(redis.clients.jedis.tests.JedisClusterTest)  Time elapsed: 0.726 sec  <<< ERROR!
redis.clients.jedis.exceptions.JedisConnectionException: Could not get a resource from the pool
	at java.net.SocksSocketImpl.remainingMillis(SocksSocketImpl.java:112)
	at java.net.SocksSocketImpl.connect(SocksSocketImpl.java:392)
	at java.net.Socket.connect(Socket.java:579)
	at redis.clients.jedis.Connection.connect(Connection.java:146)
	at redis.clients.jedis.BinaryClient.connect(BinaryClient.java:83)
	at redis.clients.jedis.BinaryJedis.connect(BinaryJedis.java:1633)
	at redis.clients.jedis.JedisFactory.makeObject(JedisFactory.java:85)
	at org.apache.commons.pool2.impl.GenericObjectPool.create(GenericObjectPool.java:861)
	at org.apache.commons.pool2.impl.GenericObjectPool.borrowObject(GenericObjectPool.java:435)
	at org.apache.commons.pool2.impl.GenericObjectPool.borrowObject(GenericObjectPool.java:363)
	at redis.clients.util.Pool.getResource(Pool.java:48)
	at redis.clients.jedis.JedisPool.getResource(JedisPool.java:85)
	at redis.clients.jedis.JedisClusterConnectionHandler.getConnectionFromNode(JedisClusterConnectionHandler.java:31)
	at redis.clients.jedis.JedisClusterCommand.runWithRetries(JedisClusterCommand.java:135)
	at redis.clients.jedis.JedisClusterCommand.run(JedisClusterCommand.java:30)
	at redis.clients.jedis.JedisCluster.set(JedisCluster.java:46)
	at redis.clients.jedis.tests.JedisClusterTest.testMigrate(JedisClusterTest.java:166)
```

```
redis.clients.jedis.tests.JedisClusterTest
testRedisClusterMaxRedirections(redis.clients.jedis.tests.JedisClusterTest)  Time elapsed: 0.802 sec  <<< ERROR!
java.lang.Exception: Unexpected exception, expected<redis.clients.jedis.exceptions.JedisClusterMaxRedirectionsException> but was<redis.clients.jedis.exceptions.JedisConnectionException>
	at java.net.SocksSocketImpl.remainingMillis(SocksSocketImpl.java:112)
	at java.net.SocksSocketImpl.connect(SocksSocketImpl.java:392)
	at java.net.Socket.connect(Socket.java:579)
	at redis.clients.jedis.Connection.connect(Connection.java:146)
	at redis.clients.jedis.BinaryClient.connect(BinaryClient.java:83)
	at redis.clients.jedis.BinaryJedis.connect(BinaryJedis.java:1633)
	at redis.clients.jedis.JedisFactory.makeObject(JedisFactory.java:85)
	at org.apache.commons.pool2.impl.GenericObjectPool.create(GenericObjectPool.java:861)
	at org.apache.commons.pool2.impl.GenericObjectPool.borrowObject(GenericObjectPool.java:435)
	at org.apache.commons.pool2.impl.GenericObjectPool.borrowObject(GenericObjectPool.java:363)
	at redis.clients.util.Pool.getResource(Pool.java:48)
	at redis.clients.jedis.JedisPool.getResource(JedisPool.java:85)
	at redis.clients.jedis.JedisClusterConnectionHandler.getConnectionFromNode(JedisClusterConnectionHandler.java:31)
	at redis.clients.jedis.JedisClusterCommand.runWithRetries(JedisClusterCommand.java:135)
	at redis.clients.jedis.JedisClusterCommand.run(JedisClusterCommand.java:30)
	at redis.clients.jedis.JedisCluster.set(JedisCluster.java:46)
	at redis.clients.jedis.tests.JedisClusterTest.testRedisClusterMaxRedirections(JedisClusterTest.java:280)
```