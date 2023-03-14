# Build SIMCA-Q applications: Basics

## Getting ready

As mentioned in the [previous section](https://github.com/OEM-Sartorius-Data-Analytics/SIMCAQ-LINUX-GUIDE/blob/main/InstallSimcaQUbuntu.md), after installing SIMCA-Q you will have on your machine the SIMCA-Q libraries you will need to link to when building your application, specifically a dynamically linked library (shared object) with a name similar to *libsimcaq.so*, as well as the required headers e.g., *SQ.h*, *SIMCAQM.h*, *SIMCAQP.h*, etc. Before starting to build your applications you should know these two locations.

### License

You will need a valid license file to be able to run your application.

#### SIMCA-Q 18

You will get a license file from Sartorius. To run your application, copy your license file to the folder where your app executable is, or to your $HOME/.umetrics directory. 

#### SIMCA-Q 17

If you did not get one directly from Sartorius, but instead you got an activation key, the process is as follows:

- You will first need the host ID of your machine. You can obtain it by running in a terminal:
```
sqhostid
```

- Then go to [https://license.x-formation.com/](https://license.x-formation.com/) and paste the activation key provided with your delivery of SIMCA-Q. This will take you to a web where you will need to provide the host ID of your machine. This will then allow you to download a license file.

Once you have the license file, you can either copy it to the folder where you main executable is or set the environment variable *SIMCAQLICENSE* to the path where your license file is.

## Build a SIMCA-Q application with GNU compilers

Installing SIMCA-Q on Linux systems provides you with a shared C library that you can link to when building your application. Thus, so far you will be limited to writing your applications in the C or C++ languages. In this section we will cover how to build these applications with the GNU compilers *gcc* (for C programs) and *g++* for (C++) programs. For examples in this section we will be referring to the *g++* compiler, but the same workflow would apply to cases where you would be working strictly with C code and where you would you the *gcc* compiler instead.

When compiling, or actually during the previous step called preprocessing, the GNU compilers should know the location of the SIMCA-Q header files. Unless they are in a standard location e.g., */usr/include/*, you would need to provide this location by passing to the compiler the *-I* flag followed by the path to the headers folder, e.g.:
```
g++ -I<headers_folder> ...
```

To build an executable application, after compiling/assemblying the code you would need to link the resulting object file(s) with the SIMCA-Q shared library. Unless it is in a standard location, at the time of linking you will need to explicetly provide its location. You can do it by passing the *-L<library_folder>* flag. You will actually need to pass the name of the SIMCA-Q shared library as well using the *-l* flag. Explicetely, you will need to pass *-lsimcaq* if the name of the library is *libsimcaq.so*. This is beacause GNU compilers already assume that libraries start with *lib* and end with *.so*. You should always make sure that the *-L* flag appears before the *-l* flag.

For the examples below, we will assume that the SIMCA-Q library is in */usr/local/lib/x86_64-gnu-linux* and the header files in */usr/local/include*.

### Simplest case: only one source code file

Let's say that you have all the source code for your SIMCA-Q application within one file, let's name it *sqsample.cpp*. In this case you can compile and link simultaneously. For instance, to get an executable named *sqsample*, just run:
```
g++ -I/usr/local/include -L/usr/local/lib/x86_64-gnu-linux -o sqsample sqsample.cpp -lsimcaq
```

If your code is bug-free, with this you would have successfully built your application. You could try to run it now by:
```
./sqsample
```

But this will fail as the SIMCA-Q library was not installed in a standard directory. This can be solved in different ways. One consist in providing the SIMCA-Q library directory by using the LD_LIBRARY_PATH environment variable. For this go to a terminal and type:
```
export LD_LIBRARY_PATH=/usr/local/lib/x86_64-gnu-linux:$LD_LIBRARY_PATH
```

A different option is to use the *-rpath* flag during the linking stage. If you are using the *g++* compiler, you could do it by:
```
g++ -I/usr/local/include -L/usr/local/lib/x86_64-gnu-linux -Wl,-rpath=/usr/lib64 -o sqsample sqsample.cpp -lsimcaq
```

However, if you are working in C and, therefore, choosing to use the *gcc* compiler, use:
```
gcc -I/usr/local/include -L/usr/local/lib/x86_64-gnu-linux -rpath=/usr/lib64 -o sqsample sqsample.c -lsimcaq
```

Now you should be able to run your application.

For simplicity, in the rest of this section we will assume that you are using the LD_LIBRARY_PATH option so compiling linking commands will not show up the *-rpath* flag.

### More than one source code files

Let's say that your main function is in a file named *main.cpp* and then you have additional classes/functions defined in *addtionalCode.h* and implemented in *additionalCode.cpp*. You could actually compile and link in the same step e.g., by typing to generate an executable named *sqsample*:
```
g++ -I/usr/local/include -L/usr/lib64 -o sqsample main.cpp additionalCode.cpp -lsimcaq
```

However, in cases where the number of source and header files increases, you will probably want to compile and like in different stages. For instance, let's assume that you have the main function in *main.cpp* and then different functions implemented in the source files *additionalCode1.cpp*, *additionalCode2.cpp* and *additionalCode3.cpp* with corresponding header files *addtionalCode1.h*, *addtionalCode2.h* and *addtionalCode3.h*. In such cases, we can instead compile every file separately into intermediate files called *object files* by passing the *-c* flag to the compiler:
```
g++ -I/usr/local/include -c additionalCode1.cpp
g++ -I/usr/local/include -c additionalCode2.cpp
g++ -I/usr/local/include -c additionalCode3.cpp
g++ -I/usr/local/include -c main.cpp
```

This will generate the object files *main.o*, *additionalCode1.o*, *additionalCode2.o* and *additionalCode3.o*. Once the object files are generated you can build the application by linking the SIMCA-Q library. For instance, to generate an executable named *sqsample*:
```
g++ -L/usr/local/lib/x86_64-gnu-linux -o sqsample main.o additionalCode1.o additionalCode2.o additionalCode3.o -lsimcaq
```

### Using *Makefiles*

When building very large programs, we need many invocations to the compilers. However, if we only make changes only to a few source code files, it is not necessary to rebuild everything; one only needs to rebuild the object files affected by the changes and link them with the rest of object files and libraries. This can be easily automated with the [make](https://www.gnu.org/software/make/manual/) utility. Actually, *make* can fulfill several other tasks like intalling applications and clean up folders for intermediate compilation files e.g., object files, once they are not needed.

To run *make* you will need a file named *Makefile* within the directory where you are running this utility. *Makefiles* are essentially a list of rules with the following structure:
```
target: dependencies
<TAB>command
<TAB>command
<TAB>command
....
[BLANK LINE]
```

*make* will only execute commands when the listed dependencies are newer than the target. In this way it ensures that rebuilds are only performed when needed. To illustrate how *make* works, let's have a look at a *Makefile* that would work for the above example:
```
all: sqsample

sqsample: main.o additionalCode1.o additionalCode3.o additionalCode3.o
    g++ -L/usr/local/lib/x86_64-gnu-linux -o sqsample main.o additionalCode1.o additionalCode2.o additionalCode3.o -lsimcaq

additionalCode1.o: additionalCode1.cpp additionalCode1.h
    g++ -I/usr/local/include -c additionalCode1.cpp

additionalCode2.o: additionalCode2.cpp additionalCode2.h
    g++ -I/usr/local/include -c additionalCode2.cpp

additionalCode3.o: additionalCode3.cpp additionalCode3.h
    g++ -I/usr/local/include -c additionalCode3.cpp

main.o: main.cpp
    g++ -I/usr/local/include -c main.cpp

.PHONY: clean
clean:
    rm *.o sqsample
```

If we run *make* without any arguments, the first target of the *Makefile* will be executed, in this case *all*. *all* has *sqsample* as a dependence. So, *make* will look for a rule to create *sqsample*. It will then find that the target *sqsample* has four dependencies, the object files *main.o*, *additionalCode1.o*, *additionalCode3.o* and *additionalCode3.o*. Subsequently, it will look for rules for these dependencies. For instance, for *additionalCode1.o* it will find the dependencies *additionalCode1.cpp* and *additionalCode1.h*. If any of these files is newer than *additionalCode1.o*, or if *additionalCode1.o* does not exist, it will execute the command *g++ -c additionalCode1.cpp*. Same will apply for all other rules.

We see a final target named *clean*. This results in that if we run *make clean*, the corresponding command will be executed i.e., *rm *.o sqsample*, implying that all object files as well as the executable *sqsample* within the directory will be removed. The command *.PHONY: clean* just ensures that this will be the behaviour even if the have in the folder a file named *clean*.


