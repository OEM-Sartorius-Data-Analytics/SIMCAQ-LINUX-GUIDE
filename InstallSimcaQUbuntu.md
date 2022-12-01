# Install SIMCA-Q on Ubuntu

The SIMCA-Q for Linux is distributed as a .rpm package and therefore can be installed directly in Linux distributions such as Red Hat Linux and CentOS directly with rpm. However, Ubuntu works
with the .deb package format instead. Whereas there are different approaches, one can convert .rpm to .deb with the "Alien" software package. "Alien" can also be used to install the package on
your Ubuntu system or, alternatively, you can do it with dpkg. More specifically:

## Installing Alien

- You will first need to add the "Universe" repository and update the package list of your system with:
```
sudo add-apt-repository universe
sudo apt-get update
```

- Then you can install the "Alien" package with:
```
sudo apt-get install alien
```


