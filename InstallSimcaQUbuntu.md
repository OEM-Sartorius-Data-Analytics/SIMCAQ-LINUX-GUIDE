# Install SIMCA-Q on Debian/Ubuntu

The SIMCA-Q for Linux is distributed as a *.rpm* package and therefore can be installed directly in Linux distributions such as Red Hat Linux and CentOS by using the rpm package manager. However, Debian-based distributions like Ubuntu work
with the *.deb* package format instead. Whereas there are different approaches, one can convert *.rpm* to *.deb* with the [Alien](https://wiki.debian.org/Alien) software package. *Alien* can also be used to install the package on
your Debina/Ubuntu system or, alternatively, you can do it with *dpkg*. Once installed, SIMCA-Q takes the shape of an available C library that you can use in your custom applications.
A more detailed guide of the installation process follows:

## Installing Alien

You will first need to add the *Universe* repository and update the package list of your system with:
```
sudo add-apt-repository universe
sudo apt-get update
```

Then you can install the *Alien* package with:
```
sudo apt-get install alien
```

## Convert the SIMCA-Q *.rpm* package to a *.deb* package and subsequent installation with *dpkg*

After purchase, you should receive from Sartorius a *.rpm* package with a name similar to *libsimcaq-17.0.0.0-1.x86_64.rpm*. In order to convert it to a *.deb* package, just type in a terminal:
```
sudo alien <name of package>.rpm
```
A *.deb* package, with a name of *libsimcaq-17.0.0.0-1.x86_64.deb* or similar, should be at this stage in your working directory.

Then, you can istall directly the *.deb* package by:
```
sudo dpkg -i <name of package>.deb
```

## Direct installation of the .rpm package

Alien also allows the direct installation of .rpm packages. For this, just type in the terminal:
```
sudo alien -i <name of package>.rpm
```

## After installation

After installation, you will should be able to find SIMCA-Q header files in */usr/include/*, with names like *SQ.h*, *SIMCAQM.h*, *SIMCAQP.h*, etc. In */usr/lib64/* you should find the libraries that
you will need to link to when compiling your application. Some help files in html format will also be available in */usr/share/doc/libsimcaq/*. Finally, some examples will be available in
*/usr/share/libsimcaq/examples/*. It is worth to check these locations, especially that of the libraries, as the specific location might be needed when compiling your SIMCA-Q applications.