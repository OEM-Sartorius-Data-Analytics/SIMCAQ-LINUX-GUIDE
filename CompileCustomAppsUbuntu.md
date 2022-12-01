# Build Custom C++/SIMCA-Q Applications in Ubuntu with GNU Autotools

## Getting ready

In */usr/share/libsimcaq/examples/* you find examples of SIMC-Q applications with files that you can use as templates. Among them, we suggest that you copy **_acinclude.m4_** to the folder of your source code.
**_acinclude.m4_** contains macros that you can use for linking SIMCA-Q libraries.

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

Then go to *https://license.x-formation.com/* and paste the activation key provided with SIMCA-Q. This will take you to a different page where you will be able to paste the host ID and download a license file. Then you can either copy the license file to the folder with your source code or set the environment variable SIMCAQLICENSE to the path where your license file is.

## An example

We will use the following application example, a kind of hello world, let's name it *sqsample.cpp*, to go through the building process:
```
#include <iostream>
#include "SQ.h"

int main()
{
  SQ_Bool bValid;

  if (SQ_IsLicenseFileValid(&bValid) != SQ_E_OK)
   {
     std::cout<<"Could not read license"<<std::endl;
   }
  else
    {
      if (!bValid)
       {
         std::cout<<"Invalid license"<<std::endl;
       }
      else
       {
         std::cout<<"Valid license"<<std::endl;
       }
    }
    
  return 0;
}
```

It's an easy to read example that will first try to find a license in your project and then find (and print) whether the license is valid or not.

At the top we can see that we included the header *SQ.h*. This is one of the headers that can be found in */usr/include/*. This is a general header that includes all other SIMCA-Q headers. Of course, you could decide to include only the headers of relevance for your application. For this, just have a look at the SIMCA-Q headers within the mentioned folder.

Building the application will require several steps. One is to generate an executable named *configure* that will probe your platform and assist in editing the Makefile. GNU autotools include the commands *aclocal* and *autoconf* that facilitates the generation of the *configure* executable. The first step consists in running the command *aclocal* from the terminal within the main application folder. *aclocal* installs a file called *aclocal.m4*, which contains the definitions of any *autoconf* macros that happen to be in use in *configure.ac* (see below). *aclocal* will include the macros defined in *acinclude.m4* automatically.

The next step is to write a file named *configure.ac* that contains several commands to be executed by *autoconf*. Let's have a look at a simple *configure.ac* that can be used within the context of our example:
```
AC_INIT([sqsample], [1.0])
AC_CONFIG_SRCDIR([sqsample.cpp])
AM_INIT_AUTOMAKE
AC_LANG([C++])
AC_PROG_CXX
AC_PROG_INSTALL
PKG_PROG_PKG_CONFIG
PKG_INSTALLDIR
PKG_CHECK_MODULES(SIMCAQ, [simcaq])
AC_SUBST([SIMCAQ_CFLAGS])
AC_SUBST([SIMCAQ_LIBS])
AC_OUTPUT(Makefile)
```

Every *configure.ac* script must call *AC_INIT* before doing anything else that produces output. Arguments for *AC_INIT* define certain macros, variables and preprocessor symbols, like in the example above where we passed as arguments the name of the package and its version.

*AC_CONFIG_SRCDIR* will tell *configure* to check for the existence of the directory of the file passed as an argument.

*AM_INIT_AUTOMAKE* runs many macros required for proper operation of the generated Makefiles.

*AC_LANG([C++])* does compilation tests using CXX and CXXCPP.

*AC_PROG_CXX* checks to see which C++ compiler you have. 

*AC_PROG_INSTALL* sets the output variable INSTALL to the name of a BSD-compatible install program, if one is found in the current PATH. 

*PKG_PROG_PKG_CONFIG* checks for an implementation of pkg-config.

*PKG_CHECK_MODULES(SIMCAQ, [simcaq])* looks for a version of SIMCA-Q, which will be hold by the SIMCAQ variable.

*AC_SUBST([SIMCAQ_CFLAGS])*

*AC_SUBST([SIMCAQ_LIBS])*

*AC_OUTPUT(Makefile)*

We can now run the *autoconf* command that will generate the *configure* executable file.

At this stage it is worth to create files named *README*, *AUTHORS*, *NEWS* and *ChangeLog*. While including these files is not critical, they are required by GNU coding standards and even more *make distcheck* will fail if they do not exist. For creating them you can just run:
```
touch README AUTHORS NEWS ChangeLog
```

The next step is to use another autotool, *automake*, to create a file named *Makefile.in* from a file name *Makefile.am* that needs to be created by you. Below you can find a simple *Makefile.am* that can be used to build our example application:
```
bin_PROGRAMS = sqsample
sqsample_CFLAGS = -pedantic @SIMCAQ_CFLAGS@
sqsample_CXXFLAGS = @SIMCAQ_CFLAGS@
sqsample_SOURCES = sqsample.cpp
sqsample_LDFLAGS = @SIMCAQ_LIBS@
sqsample_LDADD = @SIMCAQ_LIBS@
```

