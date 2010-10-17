# Welcome to the jedis wiki!

## Getting started

### Installing Jedis
In order to have Jedis as a dependency in your application you can:

#### Clone the github project and compile the code
That is very easy, you just need to:
    git clone git://github.com/xetorthio/jedis.git

And then package it:
    mvn package

This will run the tests. And the tests use two instances of the latests Redis version (you can have that by compiling the maste branch), these Redis instances should run one of the default port (6379) and the other on (6380) both with the default authentication password enabled (foobared). You can use the provided conf files in the conf folders.

#### Download the JAR from github
Just go to the Downloads section and use the latest Jedis JAR available.

#### Configure a Maven dependency
Jedis is also distributed as a Maven Dependency through Sonatype. To configure that just add the following XML snippet to your pom.xml file.

    <dependency>
        <groupId>redis.clients</groupId>
        <artifactId>jedis</artifactId>
        <version>1.3.0</version>
        <type>jar</type>
        <scope>compile</scope>
    </dependency>

