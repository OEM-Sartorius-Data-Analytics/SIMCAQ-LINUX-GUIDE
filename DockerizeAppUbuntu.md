# Create a Docker image for a SIMCA-Q app on Ubuntu

Let's Dockerize the SIMCA-Q app on Ubuntu we saw on the previous [example](CompileCustomAppsUbuntu.md).

We will first create a Docker image of Ubuntu with SIMCA-Q installed, and then use this image as a base to create another one with the SIMCA-Q app.

## Docker image of Ubuntu with SIMCA-Q installed

Within a folder with the SIMCA-Q rpm package create a Dockerfile with the following content:
```
FROM ubuntu:22.04

LABEL author="xxx"
LABEL description="Ubuntu 22.04 with SimcaQ installed"

RUN mkdir SimcaQ
COPY libsimcaq-17.0.0.0-1.x86_64.rpm /SimcaQ/

RUN apt-get update

RUN apt-get install -y software-properties-common
RUN apt-get update

RUN add-apt-repository universe
RUN apt-get update
RUN apt-get install -y alien

RUN alien -i /SimcaQ/libsimcaq-17.0.0.0-1.x86_64.rpm
```

Then build the image e.g., with name *ubuntu2204_simcaq17*:
```
docker image build -t ubuntu2204_simcaq17 .
```

# Docker image for the sample SIMCA-Q app