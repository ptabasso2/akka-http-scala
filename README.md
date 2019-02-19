# Simple Akka-http-scala app

This example shows a simple microservice written in Scala using:

- [Akka HTTP](http://doc.akka.io/docs/akka-http/current/scala/http/), to expose the http services

**Important note:** We make use of ***bindAndHandle()*** in this sample app

## Building your project

If you do not have SBT installed:

### Installing SBT

#### deb
``` 
echo "deb https://dl.bintray.com/sbt/debian /" | sudo tee -a /etc/apt/sources.list.d/sbt.list
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 2EE0EA64E40A89B84B2DF73499E82A75642AC823
sudo apt-get update
sudo apt-get install sbt
```


#### macOS (using brew)

```
brew install sbt@1
```

### Building

```
sbt clean compile
```

## Deploying the Datadog Agent and have it configured for APM

```
docker run --name datadog -d -v /var/run/docker.sock:/var/run/docker.sock:ro \
              -v /proc/:/host/proc/:ro \
              -v /sys/fs/cgroup/:/host/sys/fs/cgroup:ro \
              -p 8126:8126/tcp -p 8125:8125/udp \
              -e DD_API_KEY=<api key> \
              -e DD_APM_ENABLED=true \
              -e DD_LOG_LEVEL=debug \
              datadog/agent:latest
```


## Running the application on local machine

If you want to run the app, you must use the SBT:

Just type "sbt run" in the terminal.

```
user@machine:/Users/pejman.tabassomi$ sbt run
[info] Loading project definition from /Users/pejman.tabassomi/Downloads/akka-http-scala/project
[info] Set current project to akka-http-scala (in build file:/Users/pejman.tabassomi/Downloads/akka-http-scala/)
[info] Running com.example.QuickstartServer 
Server online at http://localhost:8080/
```

Now we want to instrument it with the Datadog agent. To do so we can actually run the following command that results from sbt:


```
user@machine:/Users/pejman.tabassomi$ java -Xms1024m -Xmx1024m -XX:ReservedCodeCacheSize=128m -XX:MaxMetaspaceSize=256m \
-javaagent:/Users/pejman.tabassomi/Downloads/akka-http-scala/dd-java-agent.jar \ 
-Ddd.agent.host=localhost -Ddd.agent.port=8126 -Ddd.service.name=akka \ 
-jar /usr/local/Cellar/sbt/1.2.8/libexec/bin/sbt-launch.jar run

[info] Loading project definition from /Users/pejman.tabassomi/Downloads/akka-http-scala/project
[info] Set current project to akka-http-scala (in build file:/Users/pejman.tabassomi/Downloads/akka-http-scala/)
[info] Running com.example.QuickstartServer 
Server online at http://localhost:8080/
```

Adding this property to set the tracer in debug mode `-Ddatadog.slf4j.simpleLogger.defaultLogLevel=debug`



## Generating activity

Inserting users
```
curl -H "Content-type: application/json" -X POST -d '{"name": "MrX", "age": 31, "countryOfResidence": "Canada"}' http://localhost:8080/users
curl -H "Content-type: application/json" -X POST -d '{"name": "MrsY", "age": 32, "countryOfResidence": "France"}' http://localhost:8080/users
```

Fetching all users
```
curl http://localhost:8080/users

```

Retrieving a specific user
```
curl http://localhost:8080/users/MrX
```

Deleting a user

```
curl -X DELETE http://localhost:8080/users/MrX
```

