# DMN - Lane assignment rule for check validation on the extranet.

## Prerequisites

You will need:
  - Java 11+ installed
  - Environment variable JAVA_HOME set accordingly
  - Maven 3.6.2+ installed
  - Docker

When using native image compilation, you will also need:
  - [GraalVM 19.3.1](https://github.com/oracle/graal/releases/tag/vm-19.3.1) installed
  - Environment variable GRAALVM_HOME set accordingly
  - Note that GraalVM native image compilation typically requires other packages (glibc-devel, zlib-devel and gcc) to be installed too.  You also need 'native-image' installed in GraalVM (using 'gu install native-image'). Please refer to [GraalVM installation documentation](https://www.graalvm.org/docs/reference-manual/aot-compilation/#prerequisites) for more details.

## Create project

```
mvn archetype:generate -DinteractiveMode=false -DarchetypeGroupId=org.kie.kogito -DarchetypeArtifactId=kogito-quarkus-archetype -DarchetypeVersion=0.9.0 -DgroupId=com.bbva -DartifactId=checks-dmn -Dversion=1.0.0
```

## Create DMN

Model the DMN and download your .dmn file in the [Business Modeler](https://kiegroup.github.io/kogito-online/#/).

Save the .dmn file in the src / main / resources directory.

## Running

#### - Compile and Run

```
mvn clean package quarkus:dev
```

#### - Package and Run in JVM mode

```
mvn clean package
java -jar target/dmn-quarkus-example-runner.jar
```

#### - Package and Run using Local Native Image

Note that this requires GRAALVM_HOME to point to a valid GraalVM installation

```
mvn clean package -Pnative
```

To run the generated native executable, generated in `target/`, execute

```
./target/checks-dmn-1.0.0-runner
```

## Containerize the application using Docker

#### - Create docker image with decision service using Local Native Image

Generate the native executable for the Docker image
```
mvn clean package -Pnative 
```

#### - Generate the Docker image

```
docker build -f Dockerfile -t dmn-lane-assigment-for-check .
```
#### - Generate the container 

```
docker run -p 8080:8080 -m 16m --cpus=.25 -d --name dmn-lane-assigment-for-check dmn-lane-assigment-for-check
```

## Testing

### Example Usage

Once the service is up and running, you can use the following example to interact with the service.

##### POST /assignationRule

Curl command (using the JSON object above):

```
curl -X POST "http://localhost:8080/assignationRule" -H "accept: application/json" -H "Content-Type: application/json" -d "{\"checkAmount\":5001}"
```

As response, lane assignation is returned.

##### Example response:

```json
{
  "checkAmount": 5001,
  "LaneName": "Carril 4"
}
```
