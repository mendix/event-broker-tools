# Business events cluster example setup

## Purpose

The examples provided in this setup is purely for illustration and is not intended to be a reference architecture for deployment of Apache Zookeeper and/or Apache Kafka.

This example file will provide a 3 node cluster of Apache Zookeeper and a 3 node cluster of Apache Kafka using version 6.2.6 from Confluent.

## Startup

If you are using `Microsoft's Visual Studio Code`:

* Clone this github repository to your local machine
* Install the `Docker for Visual Studio Code` extension by Microsoft from the Marketplace.
* Open the docker-compose.yml file.
* Right click on the docker-compose.yml file and simply select `Compose Up`

Your docker image should now be running!

## Preparing the Docker environment

You will need to have Docker installed, and Docker should have access to about 2GB of RAM.
New versions of Docker come with docker-compose integrated.

If you have a recent install you can use `docker compose up -d`, for older ones with a separate docker-compose use `docker-compose up -d`.
Depending on whether you already have the images, this could take quite some time.

## Testing

In order to test the flow of events

```bash
docker exec -it kafka-2 /bin/bash
kafka-console-producer --bootstrap-server localhost:19092,localhost:29092,localhost:39092 --topic manualtest
```

then in another console

```bash
docker exec -it kafka-1 /bin/bash
kafka-console-consumer --bootstrap-server localhost:19092,localhost:29092,localhost:39092 --topic manualtest --from-beginning
```

from where you can type messages which will appear in the other console.

## Credits

This example was created based on the examples provided by <https://github.com/confluentinc/cp-docker-images>
