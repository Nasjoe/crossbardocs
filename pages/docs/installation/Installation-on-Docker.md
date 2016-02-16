[Documentation](.) > [Installation](Installation) > [Local Installation](Local Installation) > Installation on Docker

# Installation on Docker

This recipe outlines *one* way to get the Crossbar.io "hello world" application running inside a [Docker](https://www.docker.com/) container.

We will build a container called "crossbar" based on Debian that will have the default "hello world" Crossbar.io demonstration and run it, passing through port `8080` so that a Web browser on the host can visit
the demo.

## Create The Container

Create an empty directory (for example, `~/crossbar-hello`) into which to place the following text as  `~/crossbar-hello/Dockerfile`:

```shell
# to build this container:
# docker build -t crossbar .
FROM debian:jessie

RUN apt-get update && apt-get -y install build-essential libssl-dev libffi-dev libreadline-dev libbz2-dev libsqlite3-dev libncurses5-dev python-pip python-dev
RUN pip install --upgrade cffi
RUN pip install crossbar[all]

# This creates the default "hello world" application.
RUN crossbar init --template hello:python --appdir /hello

# You need to forward port 8080 with "-p 8080" on the command-line:
# docker run -p 8080 crossbar
ENTRYPOINT ["crossbar", "start", "--cbdir", "/hello/.crossbar"]
```

Next, we need to build the Docker container, which we will tag with
the name "crossbar". The first time might take a little while, as
docker will download the base Debian jessie image.

    docker build -t crossbar ~/crossbar-hello

This should eventually output a final line like `Successfully built 9029429fc7cb`.

## Running the Container

We need to tell Docker to forward port `8080` through to the host and run our container.

    docker run -d -p 8080 crossbar

This will print out an SHA hash, which is the ID of the container. Alternatively, you can capture the container ID in an environment variable if you're running on Linux or OS X:

    export CONTAINER_ID=`docker run -d -p 8080 crossbar`

Now, we use the container ID to figure out the IP address that Docker assigned to the running container:

    docker inspect $CONTAINER_ID | grep IPAddress

(Replace $CONTAINER_ID above with whatever docker printed out in the previous step). This should print out something like:

```console
        "IPAddress": "172.17.0.47",
```

## Visit the Demo in a Browser

In your favourite browser, visit the above IP address at port `8080`, like `http://172.17.0.47:8080` and you should see a Web page with title "Hello WAMP". Note that the IP address you receive will likely
be different from the above example.


## Shut down the Container

When you're done, you need to kill off the container:

    docker kill $CONTAINER_ID

Again, replace `$CONTAINER_ID` with the actual container ID.


## Next

Ready to go? Then [choose your language or device of choice](Choose your Weapon).
