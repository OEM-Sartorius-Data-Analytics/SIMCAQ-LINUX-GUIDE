# Install SIMCA-Q on Debian/Ubuntu

- [SIMCA-Q 18](#simca-q-18).
- [SIMCA-Q 17](#simca-q-17).

## SIMCA-Q 18

SIMCA-Q 18 is distributed as a tarball: simca-q-18.0.0.tar.gz

To install SIMCA-Q, you will need to unpack the tarball. To do this, navigate to the directory where the tarball is located and run the following command:
```
tar xzf simca-q-18.0.0.tar.gz
```

By default, the contents of the tarball will be extracted to the *./usr/local* directory relative to the tarball's location. The libraries required to build an application can be found in *./usr/local/x86_64-gnu-linux/*, and the header files can be found in *./usr/local/include/*.

Note that you can choose to extract the contents of the tarball to a different directory, as long as you specify the correct paths to the libraries and headers when building your application. For example, extracting the tarball to */opt/simca-q-18/* would be valid, as long as you update your build configuration accordingly.

It's worth noting that unpacking the tarball in the root directory might have some benefits, as the */usr/local/lib* directory is typically included in the system's library search path. However, this is not required, and you can choose to unpack the tarball in any directory that you have access to.

### Documentation

After installation, you will find documentation for SIMCA-Q in *./usr/local/share/doc/*.

### Code examples

Installation will also provide code examples that you will be able to find in *./usr/local/share/libsimcaq/examples/*.

## SIMCA-Q 17

SIMCA-Q 17 for Linux is distributed as a *.rpm* package and therefore can be installed directly in Linux distributions such as Red Hat Linux and CentOS by using the rpm package manager. However, Debian-based distributions such as Ubuntu use the *.deb* package format instead. To convert *.rpm* to *.deb*, you can use the *Alien* software package. With *Alien*, you can install the package on your Debian/Ubuntu system, or you can use *dpkg* to install it. Once installed, SIMCA-Q will be available as a C library that you can use in your custom applications. To get a more detailed guide on the installation process, follow the steps below.

### Installing Alien

You will first need to add the *Universe* repository and update the package list of your system with:
```
sudo add-apt-repository universe
sudo apt-get update
```

Then you can install the *Alien* package with:
```
sudo apt-get install alien
```

### Installing SIMCA-Q

#### Convert the SIMCA-Q *.rpm* package to a *.deb* package and subsequent installation with *dpkg*

After purchase, you should receive from Sartorius a *.rpm* package with a name similar to *libsimcaq-17.0.0.0-1.x86_64.rpm*. In order to convert it to a *.deb* package, just type in a terminal:
```
sudo alien <name of package>.rpm
```
A *.deb* package, with a name of *libsimcaq-17.0.0.0-1.x86_64.deb* or similar, should be at this stage in your working directory.

Then, you can istall directly the *.deb* package by:
```
sudo dpkg -i <name of package>.deb
```

### Direct installation of the .rpm package

Alien also allows the direct installation of .rpm packages. For this, just type in the terminal:
```
sudo alien -i <name of package>.rpm
```

### After installation

After installation, you will should be able to find SIMCA-Q header files in */usr/include/*, with names like *SQ.h*, *SIMCAQM.h*, *SIMCAQP.h*, etc. In */usr/lib64/* you should find the libraries that you will need to link to when compiling your application. It is worth to check these locations, especially that of the libraries, as the specific location might be needed when compiling your SIMCA-Q applications.

Some help files in html format will also be available in */usr/share/doc/libsimcaq/*. Finally, some examples will be available in */usr/share/libsimcaq/examples/*. 