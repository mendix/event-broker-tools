# Business events local setup

This project helps to get you quickly up and running using [Mendix Business Events](https://docs.mendix.com/appstore/modules/business-events/).
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

If you have Docker v2 or above, you can use `docker compose up -d`. For Docker v1, use `docker-compose up -d`. Depending on whether you already have the images, this could take quite some time.

If you want to know for sure if kafka is ready you can run `docker exec kafka kafka-topics --list --exclude-internal --bootstrap-server localhost:19092` it list all the topics, which should be empty as long as you did not connect a Mendix app yet.

## Cleanup the Docker environment

To clean up the containers and volumes used, use `docker compose down -v` or `docker-compose down -v`.
This will also remove all the data, including any messages send.

## Setting constants for running your app locally

Set these constant values in your Mendix application.

### Windows

If you are running Docker on Windows, install the `BusinessEvent` module in your project in Studio Pro (currently it's built for version 9.18.0). In the settings of the App, add the following constants:

* `BusinessEvents.ChannelName`:`local`
* `BusinessEvents.ServerUrl`:`localhost:9092`

### MacOS or Linux
If you are on macOS or Linux, and running Docker in another environment than Mendix, you might need to specify the ip address for the server instead of relying on localhost.

#### MacOS
For Docker running on MacOS, and Studio Pro running on Windows via Parallels, use the settings below:

* `BusinessEvents.ChannelName`:`local`
* `BusinessEvents.ServerUrl`:`10.211.55.2:9094`

Note that is is not possible to run Docker from your Windows VM in Parallels. 

#### Linux
For Docker running on Linux, with Studio Pro running on Windows via VirtualBox/KVM, identify the IP address of the host that the virtual machine can access and use it for `BusinessEvents.ServerUrl`

* `BusinessEvents.ChannelName`:`local`
* `BusinessEvents.ServerUrl`:`<IP ADDRESS>:9094`


## Receiving events

You can use a different value for the `ChannelName` as the supporting topic will be automatically created.
Make sure the `BusinessEvents.Username` and `BusinessEvents.Password` are **not** set, as the local setup doesn't use them.

If you run into an error that `The requested image's platform (linux/amd64) does not match the detected host platform`, add this line to both the zookeeper `zk` and kafka sections of the yaml file:

- `platform: linux/arm64`

To be sure the events are received locally you can run (change the topic if you used a different one):

```bash
docker exec kafka /bin/kafka-console-consumer --topic local --from-beginning --bootstrap-server kafka:19092
```

Optionally add `--property print.headers=true` to also see the headers and not just the data.
This might be useful to see the `ce_sourcename` (SourceName) `ce_type` (EventName) values.
It should display all the send events in JSON, and keep listening for new events, use `Ctrl+c` to exit.

It's possible that you'll see `WARN` log messages as the container is first starting up due to timing issues. It's only an issue if these messages persist. 
