# Build Custom C++/SIMCA-Q Applications in Ubuntu with GNU tools

## Getting ready

In */usr/share/libsimcaq/examples/* you find examples of SIMC-Q applications with files that you can use as templates. Among them, we suggest that you copy **_acinclude.m4_** to the folder of your source code.
**_acinclude.m4_** contains macros needed for linking SIMCA-Q libraries.

You will also need *pkg-config*, which is usually not shipped with fresh Ubuntu installations. To install it just run:
```
sudo apt-get update -y
sudo apt-get install -y pkg-config
```

### License

In order to get a license file, you will need the host ID of your machine. You can obtain it by running:
```
sqhostid
```

Then go to *https://license.x-formation.com/* and paste the activation key provided with SIMCA-Q. This will take you to a different page where you will be able to paste the host ID and download a license file. Tehn you can either copy the license file to the folder with your source code or set the environment variable SIMCAQLICENSE to the path where your license file is.