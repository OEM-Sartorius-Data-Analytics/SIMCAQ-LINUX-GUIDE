# Build SIMCA-Q applications: Basics

## Getting ready

As mentioned in the [previous section](https://github.com/OEM-Sartorius-Data-Analytics/SIMCAQ-LINUX-GUIDE/blob/main/InstallSimcaQUbuntu.md), after installing SIMCA-Q on your machine you should be able to find the SIMCA-Q libraries you will need to link when building your application in */usr/lib64/*, specifically a dynamically linked library (shared object) with a name similar to *libsimcaq.so*. In */usr/include/* header files like *SQ.h*, *SIMCAQM.h*, *SIMCAQP.h*, etc. Nevertheless, locations might vary depending on your specific case, so you should check in advance.

### License

Tou will also need a valid license file to be able to run your application. If you did not get one directly from Sartorius, but instead you got an activation key, the process is as follows:

- You will first need the host ID of your machine. You can obtain it by running in a terminal:
```
sqhostid
```

- Then go to [https://license.x-formation.com/](https://license.x-formation.com/) and paste the activation key provided with your delivery of SIMCA-Q. This will take you to a web where you will need to provide the host ID of your machine. This will then allow you to download a license file.

Once you have the license file, you can either copy it to the folder where you main executable is or set the environment variable SIMCAQLICENSE to the path where your license file is.

## Build a simple SIMCA-Q application

To build an executable application out of it you will need to compile the code and link the resulting object file(s) with the SIMCA-Q library. As mentioned above, at least in latest Ubuntu versions you will find the library in */usr/lib64/*, which is not at the time of writing a standard location where GNU compilers will look for libraries. Thus, you will need to explicetly provide the location of the library. The SIMCA-Q header files should be in a standard location though, */usr/include/*, so unless this changes in your setup you will not need to provide them at the time of linking.

Let's say that you have all the source code for your SIMCA-Q application within one single file, let's name *sqsample.cpp*. In this case you can compile and link simultaneously. For instance, to get an executable named *sqsample*, just run:
```
g++ -o sqsample sqsample.cpp -L/usr/lib64 -lsimcaq
```

In case your source code is within more than one file you will need to compile and link separately. Let's say that your main function is in a file named *main.cpp* and then you have additional classes/functions defined in *addtionalCode.h* and implemented in *additionalCode.cpp*.
