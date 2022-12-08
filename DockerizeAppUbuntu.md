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

## Docker image for the sample SIMCA-Q app

Create a folder that contains the following files:

- The code for the sample app, *sqsample.cpp*.
- The files *acinclude.m4*, *configure.ac*, *Makefile.am* detailed in the previous [example](CompileCustomAppsUbuntu.md).
- You need to include also a license file *license_name.$Q*. As SIMCA-Q will recognize containers built from different images as different, you will need a specific license that will not check the host id. Contact Sartorius for this possibility.

Then, create a Dockerfile with the following content:
```
FROM ubuntu2204_simcaq17

RUN mkdir SampleApp

COPY ./* ./SampleApp/

# RUN apt-get install -y pkg-config

WORKDIR SampleApp

RUN aclocal

RUN touch README AUTHORS NEWS ChangeLog

RUN autoconf

RUN automake -a

RUN ./configure PKG_CONFIG_PATH=/usr/lib64/pkgconfig

RUN export LD_LIBRARY_PATH=/usr/lib64

RUN make

# If you want your containers to run automatically the app:
# CMD ./sqsample
```