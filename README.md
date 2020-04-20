---
tags: Confluent & Kafka
---

# Hello Word from Intellij With Kafka

> Github https://github.com/dadinjaenudin/Hello-Word-from-Intellij-With-Kafka

# Install Mave
Download Apache Maven
> Extract zip 
> Edit Path and Environment variable to maven\bin


# Install and prepare IntelliJ

Download and install IntelliJ;

> Install the IntelliJ IDEA Scala Plugin (Menu Item File | Settings -> Plugins -> Browse Repositories -> Search for Scala);

Setting maven home directory
File - Setting
![](https://i.imgur.com/sanThXd.png)


# Import Directory 

- Open IntelliJ IDEA 
- Open or Import
- Choose directory

# Build Jar file
![](https://i.imgur.com/byCjD6R.png)


## Lets create hello word from kafka with IntelliJ

Make sure docker confluent is up and running

$ docker exec -it broker bash
$ cd /usr/bin
$ ./kafka-console-consumer --bootstrap-server broker:29092 --topic testTopic

We can test consumer with sending data from producer to run this kafka script
$ cd /usr/bin
$ ./kafka-console-producer --broker-list broker:29092 --topic testTopic


# Lets create producer from IntelliJ using java
- Open the IDE
- File - New Project - Choose Maven
![](https://i.imgur.com/7bulIWM.png)

- Choose Next modified the name of project like below
![](https://i.imgur.com/guMr2eS.png)

- go ahead clik Finish 

- Now edit file pom.xml
```
   <properties>
        <java.version>1.8</java.version>
        <kafka.version>2.1.1</kafka.version>
    </properties>

    <build>
        <plugins>
            <!-- Maven Compiler Plugin-->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.0</version>
                <configuration>
                    <source>${java.version}</source>
                    <target>${java.version}</target>
                </configuration>
            </plugin>
        </plugins>
    </build>

    <dependencies>
        <!-- Apache Kafka Clients-->
        <dependency>
            <groupId>org.apache.kafka</groupId>
            <artifactId>kafka-clients</artifactId>
            <version>${kafka.version}</version>
        </dependency>

        <!-- Apache Log4J2 binding for SLF4J -->
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-slf4j-impl</artifactId>
            <version>2.11.0</version>
        </dependency>

    </dependencies>
```
- Edit  hello-word-producer.iml
```
<?xml version="1.0" encoding="UTF-8"?>
<module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">
  <component name="NewModuleRootManager" LANGUAGE_LEVEL="JDK_1_8">
    <output url="file://$MODULE_DIR$/target/classes" />
    <output-test url="file://$MODULE_DIR$/target/test-classes" />
    <content url="file://$MODULE_DIR$">
      <sourceFolder url="file://$MODULE_DIR$/src/main/java" isTestSource="false" />
      <sourceFolder url="file://$MODULE_DIR$/src/main/resources" type="java-resource" />
      <excludeFolder url="file://$MODULE_DIR$/target" />
    </content>
    <orderEntry type="inheritedJdk" />
    <orderEntry type="sourceFolder" forTests="false" />
    <orderEntry type="library" name="Maven: org.apache.kafka:kafka-clients:2.1.1" level="project" />
    <orderEntry type="library" name="Maven: com.github.luben:zstd-jni:1.3.7-1" level="project" />
    <orderEntry type="library" name="Maven: org.lz4:lz4-java:1.5.0" level="project" />
    <orderEntry type="library" name="Maven: org.xerial.snappy:snappy-java:1.1.7.2" level="project" />
    <orderEntry type="library" name="Maven: org.slf4j:slf4j-api:1.7.25" level="project" />
    <orderEntry type="library" name="Maven: org.apache.logging.log4j:log4j-slf4j-impl:2.11.0" level="project" />
    <orderEntry type="library" name="Maven: org.apache.logging.log4j:log4j-api:2.11.0" level="project" />
    <orderEntry type="library" scope="RUNTIME" name="Maven: org.apache.logging.log4j:log4j-core:2.11.0" level="project" />
  </component>
</module>
```


- go to directory src - main - resources - righ click - new file - name : log4j2.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<!--
  ~ Copyright (c) 2018. Prashant Kumar Pandey
  ~
  ~ Licensed under the Apache License, Version 2.0 (the "License");
  ~ You may not use this file except in compliance with the License.
  ~ You may obtain a copy of the License at
  ~
  ~ http://www.apache.org/licenses/LICENSE-2.0
  ~
  ~ Unless required by applicable law or agreed to in writing,
  ~ software distributed under the License is distributed on an "AS IS" BASIS,
  ~ WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  ~ See the License for the specific language governing permissions and limitations under the License.
  -->

<Configuration status="ERROR">
    <Appenders>
        <Console name="stdout" target="SYSTEM_OUT">
            <PatternLayout pattern="[%d] (%c) - %p %m %n"/>
        </Console>
    </Appenders>
    <Loggers>
        <Root level="error">
            <AppenderRef ref="stdout"/>
        </Root>
        <Logger name="org.apache.kafka.clients" level="warn" additivity="false">
            <AppenderRef ref="stdout"/>
        </Logger>
        <Logger name="guru.learningjournal.kafka.examples" level="trace" additivity="false">
            <AppenderRef ref="stdout"/>
        </Logger>
    </Loggers>
</Configuration>

```
    
- Moving forward to create package,  go to src - main - java - right click - New - Package - Name : org.djsoft.kafka.learning
it will look like below
![](https://i.imgur.com/JHXXVaf.png)

- After the package already created we can create java class, go ahead right click and New - Java Class -
Name : HelloKafkaProducer and copy this script 

```
package org.djsoft.kafka.learning;

import org.djsoft.kafka.learning.common.AppConfigs;
import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.ProducerConfig;
import org.apache.kafka.clients.producer.ProducerRecord;
import org.apache.kafka.common.serialization.IntegerSerializer;
import org.apache.kafka.common.serialization.StringSerializer;
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;

import java.util.Properties;

/**
 * A Kafka producer that sends numEvents (# of messages) to a given topicName
 *
 * @author prashant
 * @author www.learningjournal.guru
 */
public class HelloKafkaProducer {
    private static final Logger logger = LogManager.getLogger(HelloKafkaProducer.class);

    public static void main(String[] args) {

        logger.info("Creating Kafka Producer...");
        Properties props = new Properties();
        props.put(ProducerConfig.CLIENT_ID_CONFIG, AppConfigs.applicationID);
        props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, AppConfigs.bootstrapServers);
        props.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, IntegerSerializer.class.getName());
        props.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());

        KafkaProducer<Integer, String> producer = new KafkaProducer<>(props);

        logger.info("Start sending messages...");
        for (int i = 1; i <= AppConfigs.numEvents; i++) {
            producer.send(new ProducerRecord<>(AppConfigs.topicName, i, "Simple Message-" + i));
        }

        logger.info("Finished - Closing Kafka Producer.");
        producer.close();

    }
}

```

- We almost finish but before running the application we will create java configuration to store topic, IP etc. To do that  right click in package org.djsoft.kafka.learning - New Package - Name org.djsoft.kafka.learning.common and then after created right click in org.djsoft.kafka.learning.common - New - Java class - Name : AppConfigs

```
package org.djsoft.kafka.learning.common;

public class AppConfigs {

    public final static String applicationID = "HelloKafkaProducer";
    //public final static String bootstrapServers = "localhost:9092,localhost:9093";
    public final static String bootstrapServers = "localhost:9092";
    public final static String topicName = "test";
    public final static int numEvents = 10;
}

```

- Lets run HelloKafkaProducer and we can see here from consumer the message 
![](https://i.imgur.com/Jf9o6wd.png)


And finaly lets build our project using menu Maven from here
![](https://i.imgur.com/lRiXPCz.png)
the jar file is created but when executed from terminal there is warning like this
![](https://i.imgur.com/QouqHZN.png)


Here is another option 
1. Copy or create directory META-INF in src - main - resources and then under directory META-INF create file name MANIFEST.MF and paste this script
```
Manifest-Version: 1.0
Main-Class: org.djsoft.kafka.learning.HelloKafkaProducer
```
it will look like below
![](https://i.imgur.com/3iuNR3K.png)


2. Go to File - Project Structure - Artifact

![](https://i.imgur.com/bITrEMo.png)

4. Add/+ - Jar - From Module with dependencies - OK
![](https://i.imgur.com/65HjzSZ.png)

6. Go to Build - Build Artifacts - Action - Build and go ahead copy the file and run from terminal
![](https://i.imgur.com/i6kff45.png)

8. Run From Terminal 
	![](https://i.imgur.com/8iz41pL.png)


## Push to github
- Create Repository in github
- From Menu VCS - Git - Remote
  ![](https://i.imgur.com/wNUHP3A.png)
- From Menu VCS - Git - Push
![](https://i.imgur.com/7co5fqT.png)



That's it for now see you next time

Happy learning guys!
