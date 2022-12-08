# Build Custom C++/SIMCA-Q Applications in Ubuntu with GNU Autotools

## Getting ready

In */usr/share/libsimcaq/examples/* you find examples of SIMC-Q applications with files that you can use as templates. Among them, we suggest that you copy **_acinclude.m4_** to the folder of your source code.
**_acinclude.m4_** contains macros that you can use for linking SIMCA-Q libraries.

You will also need *pkg-config*. *pkg-config* provides the necessary details for compiling and linking a program to a library. This metadata is stored in pkg-config files. After installing SIMCA-Q in Ubuntu, you should be able to find in */usr/lib64/pkgconfig/* a file named *simcaq.pc* that will help you to link SIMCA-Q to your custom application. *pkg-config* is usually not shipped with fresh Ubuntu installations. To install it just run:
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

### Building the application

Building the application will require several steps. One is to generate an executable named *configure* that will probe your platform and assist in editing the Makefile. GNU autotools include the commands *aclocal* and *autoconf* that facilitates the generation of the *configure* executable. The first step consists in running the command *aclocal* from the terminal within the main application folder. *aclocal* installs a file called *aclocal.m4*, which contains the definitions of any *autoconf* macros that happen to be in use in *configure.ac* (see below). *aclocal* will include the macros defined in *acinclude.m4* automatically.

#### A simple configure.ac file

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
AC_CONFIG_FILES([Makefile])
AC_OUTPUT
```

Every *configure.ac* script must call *AC_INIT* before doing anything else that produces output. Arguments for *AC_INIT* define certain macros, variables and preprocessor symbols, like in the example above where we passed as arguments the name of the package and its version.

*AC_CONFIG_SRCDIR* will tell *configure* to check for the existence of the directory of the file passed as an argument.

*AM_INIT_AUTOMAKE* runs many macros required for proper operation of the generated Makefiles.

*AC_LANG([C++])* does compilation tests using CXX and CXXCPP.

*AC_PROG_CXX* checks to see which C++ compiler you have. 

*AC_PROG_INSTALL* sets the output variable INSTALL to the name of a BSD-compatible install program, if one is found in the current PATH. If not then it uses install-sh which automake will install at the root of your package directory if it's not there yet. 

*PKG_PROG_PKG_CONFIG* checks for an implementation of pkg-config.

*PKG_CHECK_MODULES(SIMCAQ, [simcaq])* looks for a version of SIMCA-Q, which will be hold by the SIMCAQ variable.

By including *AC_SUBST([SIMCAQ_CFLAGS])* and *AC_SUBST([SIMCAQ_LIBS])*, every occurrence of @SIMCAQ_CFLAGS@ and @SIMCAQ_LIBS@ in all files used at later stages will be substituted by the values for Libs and Cflags defined in */usr/lib64/pkgconfig/simcaq.pc*.

*AC_CONFIG_FILES([Makefile])* will make *AC_OUTPUT* create a Makefile.

*AC_OUTPUT*: every *configure.ac* should finish by calling this macro, which will generate a file named *config.status* and launch it. *config.status* will then perform all configuration actions.

#### A simple Makefile.am file

The next step is to use another autotool, *automake*, to create a file named *Makefile.in* from a file name *Makefile.am* that needs to be created by you. Below you can find a simple *Makefile.am* that can be used to build our example application:
```
bin_PROGRAMS = sqsample
sqsample_CFLAGS = -pedantic @SIMCAQ_CFLAGS@
sqsample_CXXFLAGS = @SIMCAQ_CFLAGS@
sqsample_SOURCES = sqsample.cpp
sqsample_LDFLAGS = @SIMCAQ_LIBS@
sqsample_LDADD = @SIMCAQ_LIBS@
```

*bin_PROGRAMS = sqsample* specifies the name of the final executable i.e., *sqsample* in this case.

In *Makefile.am*, associated with each program are several assisting variables that are named after the program name followed by underscore e.g. in our case *sqsample_<VariableName>*.

The *CFLAGS* assignment ...

The *CXXFLAGS* assignment adds compilation flags to be used when compiling CXX (C++) files.

*sqsample_SOURCES = sqsample.cpp* specifies the source file that composes the program. In our case it was just *sqsample.cpp*, but here you should include any other files and headers of your app.

The *LDFLAGS* assignment is where you insert the -L flags that are needed by the compiler when it links all the object files to an executable.

The *LDADD* assignment is where you list a long set of installed libraries that you want to link in with all of your executables. 

#### Using autotools to build the executable

With *configure.ac* ready, we run the *aclocal* command:
```
aclocal
```

*aclocal* generates, based on the contents of *configure.ac*, a file named *aclocal.m4* that includes several macros needed to build your app. *aclocal.m4* wil automatically include the contents of *acinclude.m4*.

We can now run the *autoconf* command that will generate the *configure* executable file:
```
autoconf
```

At this stage it is worth to create files named *README*, *AUTHORS*, *NEWS* and *ChangeLog*. While including these files is not critical, they are required by GNU coding standards and even more *make distcheck* will fail if they do not exist. For creating them you can just run:
```
touch README AUTHORS NEWS ChangeLog
```

You can then create *Makefile.in* simply by running:
```
automake -a
```

For creating the *Makefile*, run:
```
./configure PKG_CONFIG_PATH=/usr/lib64/pkgconfig
```

To be able to run the application you need to have the path to your SIMCA-Q libraries in your LD_LIBRARY_PATH variable. LD_LIBRARY_PATH tois an environmental variable that tells dynamic link loader, a small program that begins all your applications, where to look for dynamic shared libraries that an application was linked with. Do this on the command-line:
```
export LD_LIBRARY_PATH=/usr/lib64
```

Now you can create the executable of your application just by running *make*:
```
make
```

Finally, you can run your app from the command line:
```
./sqsample
```
