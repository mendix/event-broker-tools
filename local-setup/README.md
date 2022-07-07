# Business events local setup

This project helps to get you quickly up and running using business events in Mendix.
The main component is a Docker compose file, so you quickly have the local resources available to support using business events.

Should you need to install Docker on your local machine, you can get downloads and instructions [here](https://docs.docker.com/engine/install/)

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

If you want to know for sure if kafka is ready you can run `docker exec kafka kafka-topics --list --exclude-internal --bootstrap-server localhost:19092` it list all the topics, which should be empty as long as you did not connect a Mendix app yet.

## Cleanup the Docker environment

To clean up the containers and volumes used, use `docker compose down -v` or `docker-compose down -v`.
This will also remove all the data, including any messages send.

## Configuring the module for local setup

If you are running Docker on Windows:

Install the `BusinessEvent` module in your project in Studio Pro (currently it's build for version 9.0.5).
In the settings of the App, add the following constants:

- `BusinessEvents.ChannelName`:`local`
- `BusinessEvents.ServerUrl`:`localhost:9092`

If you are on macOS or Linux, and running Docker in another environment than Mendix, you might need to specify the ip address for the server instead of relying on localhost.
For Docker running on MacOS, and Studio Pro running on Windows via Parallels, use the settings below:

- `BusinessEvents.ChannelName`:`local`
- `BusinessEvents.ServerUrl`:`10.211.55.2:9094`

For Docker running on Linux, with Studio Pro running on Windows via VirtualBox/KVM, identify the IP address of the host that the virtual machine can access and use it for `BusinessEvents.ServerUrl`

- `BusinessEvents.ChannelName`:`local`
- `BusinessEvents.ServerUrl`:`<IP ADDRESS>:9094`

You can use a different value for the `ChannelName` as the supporting topic will be automatically created.
Make sure the `BusinessEvents.Username` and `BusinessEvents.Password` are **not** set, as the local setup doesn't use them.

To be sure the events are received locally you can run (change the topic if you used a different one):

```bash
docker exec kafka /bin/kafka-console-consumer --topic local --from-beginning --bootstrap-server kafka:19092
```

Optionally add `--property print.headers=true` to also see the headers and not just the data.
This might be useful to see the `ce_sourcename` (SourceName) `ce_type` (EventName) values.
It should display all the send events in JSON, and keep listening for new events, use `Ctrl+c` to exit.
