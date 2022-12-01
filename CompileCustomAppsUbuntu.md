# Build Custom C++/SIMCA-Q Applications in Ubuntu with GNU tools

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
#include "SQ.h"

int main()
{
  std::cout<<"Hi! "<<sqrt(100)<<std::endl;

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

configure.ac:
```
AC_INIT([sqsample0], [1.0])
AC_CONFIG_SRCDIR([sqsample0.cpp])
AM_INIT_AUTOMAKE
AC_CONFIG_HEADERS([config.h])
AC_LANG(C)
AC_PROG_CC
AC_PROG_CXX
AC_PROG_INSTALL
AM_PROG_CC_C_O
PKG_PROG_PKG_CONFIG
PKG_INSTALLDIR
PKG_CHECK_MODULES(SIMCAQ, [simcaq])
AC_SUBST([SIMCAQ_CFLAGS])
AC_SUBST([SIMCAQ_LIBS])
AC_OUTPUT
```

Makefile.am:
```
bin_PROGRAMS = sqsample
sqsample_CFLAGS = -pedantic @SIMCAQ_CFLAGS@
sqsample_CXXFLAGS = @SIMCAQ_CFLAGS@
sqsample_SOURCES = sqsample0.cpp
sqsample_LDFLAGS = @SIMCAQ_LIBS@
sqsample_LDADD = @SIMCAQ_LIBS@
```

