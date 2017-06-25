# jnilink
Learning to do JNI and use native windows lib to capture key presses. 
The issues listed in this README.md are about the problems I encountered from testing this.

Done on windows 7 SP1

# Generating the C header file

```
cd C:\Users\Undefined\git\global-key-listener\target\classes
javah -jni -classpath "C:\Users\Undefined\git\global-key-listener\target\classes" com.com.lleclerc.jnilink.SayHelloMain
```

Issue #1 : Bad usage of javah (Must not call it from current directory, go up the package folders)
```
Error: Could not find class file for 'SayHelloMain'
```

Issue #2 : Bad usage of javah (Must not specifie .class)
```
Exception in thread "main" java.lang.IllegalArgumentException: Not a valid class
 name: com.lleclerc.jnilink.SayHelloMain.class
        at com.sun.tools.javac.api.JavacTool.getTask(JavacTool.java:129)
        at com.sun.tools.javac.api.JavacTool.getTask(JavacTool.java:107)
        at com.sun.tools.javac.api.JavacTool.getTask(JavacTool.java:64)
        at com.sun.tools.javah.JavahTask.run(JavahTask.java:503)
        at com.sun.tools.javah.JavahTask.run(JavahTask.java:329)
        at com.sun.tools.javah.Main.main(Main.java:46)
```
Resulted in file: git\global-key-listener\NativeC\com\_lleclerc\_jnilink_SayHelloMain.h

# Building the C app

* Installed an IDE Eclipse C/C++ (http://www.eclipse.org/downloads/packages/eclipse-ide-cc-developers/mars1)
* Installed MinGW & MSYS (mingw-get-setup.exe - https://sourceforge.net/projects/mingw/files/Installer/) 
    * It's a tool to download the actual packages we want (dont uncheck the user interface)
    * Open it from the icon on desktop and go in Basic Setup, and "Mark for installations" the packages "mingw-developer-toolkit", "mingw32-base" and "msys-base".
    * Added C:\MinGW\bin to environment variable PATH and restarted eclipse CDT.
* Restart eclipse and create new shared lib with "MinGW GCC".

Issue #1 : Which "Project type" to pick.
Looked it up on the net, and it is a "shared library". Difference between shared and static is that static will add it at compile time while shared is loaded at execution time.

Issue #2 : Which "Toolchains" to pick.
There was "Cross GCC", "Cygwin GCC" and "Microsoft Visual C++". I had issues then looked it up and eclipse CDT recommend to use something else, which is "MinGW GCC".
```
Cannot run program "cl": Launching failed

Error: Program "cl" not found in PATH
```

Issue #3 : Can't find jni.h
```
Info: Internal Builder is used for build
gcc -O0 -g3 -Wall -c -fmessage-length=0 -o AnswerHelloCVersion.o "..\\AnswerHelloCVersion.c" 
..\AnswerHelloCVersion.c:1:17: fatal error: jni.h: No such file or directory
compilation terminated.
```
I added the references (Right+Click on Project -> Properties -> C/C++ General -> Paths and Symbols -> Includes tab)
"C:\Program Files\Java\jdk1.8.0\_65\include" and "C:\Program Files\Java\jdk1.8.0_65\include\win32"

Issue #4 : Can't find the library
```
Exception in thread "main" java.lang.UnsatisfiedLinkError: no lib/libAnswerHelloMinGWSharedLib.dll in java.library.path
	at java.lang.ClassLoader.loadLibrary(ClassLoader.java:1864)
	at java.lang.Runtime.loadLibrary0(Runtime.java:870)
	at java.lang.System.loadLibrary(System.java:1122)
	at com.lleclerc.jnilink.SayHelloMain.<clinit>(SayHelloMain.java:6)
```
Fixed this by setting the workspace location "jnilink/NativeC" in both Native libraries locations for test and run.
* Right+Click on project -> Build Path.. -> Configure Build Path -> jnilink/src/main/java -> Native libraries location
* Right+Click on project -> Build Path.. -> Configure Build Path -> jnilink/src/test/java -> Native libraries location

Issue #5 : The library isn't in 64-bit.
```
Exception in thread "main" java.lang.UnsatisfiedLinkError: C:\Users\Undefined\git\global-key-listener\NativeC\libAnswerHelloMinGWSharedLib.dll: Can't load IA 32-bit .dll on a AMD 64-bit platform
	at java.lang.ClassLoader$NativeLibrary.load(Native Method)
	at java.lang.ClassLoader.loadLibrary0(ClassLoader.java:1938)
	at java.lang.ClassLoader.loadLibrary(ClassLoader.java:1854)
	at java.lang.Runtime.loadLibrary0(Runtime.java:870)
	at java.lang.System.loadLibrary(System.java:1122)
	at com.lleclerc.jnilink.SayHelloMain.<clinit>(SayHelloMain.java:6)
```

## Build the project in Eclipse
## 