## Trusty AI - Integration tests

This project contains Cypress test suites to verify UI of Trusty AI. 

### Run integration tests with mocked data

This is a basis test suite which should verify that UI of Trusty AI is running and mocked data are shown as expected. 

#### Steps

To install dependencies
```
yarn install
```

To run Cypress test suite with mocked data
```
yarn run test:it
```

### Run integration tests with real data

This is e2e test suite. Each test communicates with UI of Trusty AI as real user of kogito application.

#### Requirements

- docker version > 19.03.12
- java version > 11
- maven version > 3.6.3
- docker-compose version > 1.25.2

Note: also previous versions of `docker` and `docker-compose` might work, but they were not tested.

#### Perform e2e test suite

To install dependencies
```
yarn install
```

To build KIE Docker images
```
yarn run build:services
```

To run Cypress test suite with real data
```
yarn run test:e2e
```

#### Manual steps
First, you should be sure that there are no conficts in docker images:
```
docker images
```

Remove obsolete image:
```
docker rmi $(docker images 'org.kie.kogito/integration-tests-trusty-service-quarkus' -a -q)
```

Or clear all docker images:
```
docker system prune --all
docker volume prune
```

Now you need to build all docker images from this kogito project, go to 'kogito-apps' folder
```
mvn clean install -DskipTests
```

Go to the docker-compose folder:
```
cd ui-packages/packages/trusty/docker-compose
```

To be sure that you have built all necessary docker images. Check file docker-compose.yml and output of this command:
```
docker images
```
You can perform previous steps by [this Bash script](docker-compose/build.sh). It deletes previous images of SNAPSHOT version and build new ones.


Run this project:
```
docker-compose up
```
You can perform previous steps by [this Bash script](docker-compose/start_docker.sh). It starts services.

#### User interaction

Send REST Request which starts this [DMN asset](https://kiegroup.github.io/kogito-online/?file=https://raw.githubusercontent.com/kiegroup/kogito-apps/master/apps-integration-tests/integration-tests-trusty-service/integration-tests-trusty-service-common/src/main/resources/TrafficViolation.dmn#/editor/dmn):
```
curl -H "Content-Type: application/json" -X POST -d "{\"Driver\":
{\"State\":\"aa\",\"City\":\"bb\",\"Age\":25,\"Points\":13}
,\"Violation\":{\"Type\":\"speed\",\"Actual Speed\":105,\"Speed Limit\":80}}" http://localhost:8080/Traffic%20Violation
```

Open [http://localhost:1338](http://localhost:1338) to view Trusty Console in the browser.

#### Stop service
To stop this project press CTRL+C and run command:
```
docker-compose down
```