# VSCode C Tutorial

Simple beginner's guide to using the Visual Studio Code C/C++ debugger and an
address sanitizer in Linux. Everything mentioned works for C++ as well, just
change gcc/clang to g++/clang++ wherever necessary and use your C++ source
files.

## Debugger

0. Open this folder with VSCode (File -> Open Folder -> ...)

1. Make sure you have the [C/C++ Extension Pack](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools-extension-pack) installed.

2. Install the necessary tools.

   If you are on a Debian-based distro:

   ```
   sudo apt install build-essentials
   ```

   If you are on a Arch-based distro:

   ```
   sudo pacman -S base-devel
   ```

   If you are on a RHEL based distro:

   ```
   sudo dnf install "@Development Tools"
   ```

3. Create a source file with the following contents. We will use this file to test the debugger and address sanitizer.

   ```c
   // main.c
   #include <stdio.h>
   #include <string.h>

   int main(int argc, char** argv)
   {
   if (argc >= 2)
   {
       printf("Hello, %s\n", argv[1]);
       return 0;
   }

   // Force a segfault
   memset(NULL, 32, 32); // Boom!

   return 0;
   }
   ```

4. Write a basic Makefile

   ```makefile
   # Makefile

   main: main.c
       gcc -g3 -Og -Wall -pedantic -o main main.c
   ```

5. Create a folder called `.vscode` at the root of the project space.

6. Create a file `.vscode/launch.json` with the following contents:

   ```json
   {
     // Use IntelliSense to learn about possible attributes.
     // Hover to view descriptions of existing attributes.
     // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
     "version": "0.2.0",
     "configurations": [
       {
         "name": "(gdb) Launch",
         "type": "cppdbg",
         "request": "launch",
         "program": "${workspaceFolder}/main",
         "args": [],
         "stopAtEntry": false,
         "cwd": "${workspaceFolder}",
         "environment": [],
         "externalConsole": false,
         "MIMode": "gdb",
         "setupCommands": [
           {
             "description": "Enable pretty-printing for gdb",
             "text": "-enable-pretty-printing",
             "ignoreFailures": true
           },
           {
             "description": "Set Disassembly Flavor to Intel",
             "text": "-gdb-set disassembly-flavor intel",
             "ignoreFailures": true
           }
         ]
       }
     ]
   }
   ```

   > You can customize this to target any executable name by editing the
   > `program` item. You can also add CLI arguments by editing the `args` list.
   > This is equivalent to passing each item separated by a space on the commad
   > line. Give it a try.

7. Navigate to a terminal and compile your program.

   ```cmd
   make
   ```

8. Start the debugger by pressing F5 in VSCode. The debugger will automatically
   break at the location of the segmentation fault.

## Address Sanitizer

Alternatively, you can use an address sanitizer to catch memory leaks, and invalid memory accesses.

> Using an address sanitizer with the VScode debugger usually will not work. To
> switch between the two, disable the address sanitizer and recompile before
> trying to use the debugger.

1. Create a C file with the following contents.

   ```c
   // main.c
   #include <stdio.h>
   #include <string.h>

   int main(int argc, char** argv)
   {
   if (argc >= 2)
   {
       printf("Hello, %s\n", argv[1]);
       return 0;
   }

   // Force a segfault
   memset(NULL, 32, 32); // Boom!

   return 0;
   }
   ```

2. Write a slightly more complex Makefile with a target for with and without
   the address sanitizer.

   ```makefile
   # Makefile

   .PHONY: main debug

   all: main

   main: main.c
       gcc -g3 -Og -Wall -pedantic -o main main.c

   debug: main.c
       gcc -g3 -Og -Wall -pedantic -fsanitize=address,undefined,leak -o main main.c
   ```

3. Compile and run. The new Makefile adds a second `debug` target which
   compiles with the address sanitizer.

   ```cmd
   $ make

   $ ./main
     [1]    11272 segmentation fault (core dumped)  ./main

   $ make debug

   $ ./main
     main.c:14:3: runtime error: null pointer passed as argument 1, which is declared to neverbe null
     AddressSanitizer:DEADLYSIGNAL
     =================================================================
     ==11571==ERROR: AddressSanitizer: SEGV on unknown address 0x000000000000 (pc 0x7fae35f7d259 bp 0x000000000001 sp 0x7ffc4da6f928 T0)
     ==11571==The signal is caused by a WRITE memory access.
     ==11571==Hint: address points to the zero page.
         #0 0x7fae35f7d259  (/usr/lib/libc.so.6+0x167259) (BuildId: 2f005a79cd1a8e385972f5a102f16adba414d75e)
         #1 0x5593b702e25b in main /home/joshua/repos/vscode-c-tutorial/main.c:14
         #2 0x7fae35e3984f  (/usr/lib/libc.so.6+0x2384f) (BuildId: 2f005a79cd1a8e385972f5a102f16adba414d75e)
         #3 0x7fae35e39909 in __libc_start_main (/usr/lib/libc.so.6+0x23909) (BuildId: 2f005a79cd1a8e385972f5a102f16adba414d75e)
         #4 0x5593b702e0f4 in _start (/home/joshua/repos/vscode-c-tutorial/main+0x10f4) (BuildId: 9a808a76b2632b10eb243ccfa68ed3f2ffa39e05)

     AddressSanitizer can not provide additional info.
     SUMMARY: AddressSanitizer: SEGV (/usr/lib/libc.so.6+0x167259) (BuildId: 2f005a79cd1a8e385972f5a102f16adba414d75e)
     ==11571==ABORTING
   ```

   > Within this output you can see the file and line number the error occurred
   > and a description of what has gone wrong, in this case a SEGV (segmentation
   > fault) on unknown adress 0x00.. aka the NULL pointer (`(void)0`).
