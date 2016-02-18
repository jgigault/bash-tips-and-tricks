# What is a Shell script

A Shell script is a simple file that contains ASCII text intended to be interpreted by the program **bash**, allowing you to run a logical sequence of commands by calling a simple line command instead of typing them one at a time in the shell.

The first line of a script should always tell the shell which program should interpret it:

    #!/bin/bash
    echo "Hello World, I am a 42 student!"

In this case, the script should be interpreted by **/bin/bash** and say Hello when it is invoked.
To invoke the script, you can type `sh scriptname` or - in a better way - make it executable with a `chmod`:

    [shell prompt]$ chmod 755 scriptname
    [shell prompt]$ ./scriptname

Add your commands one at a line in the script file and call external programs as you do in the shell.
Bash has a lot of builtin functions and enables you to write some real applications with variables, conditions, loops, functions...