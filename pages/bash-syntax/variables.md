## Variables

The syntax of the variables in Bash programming language is flexible. Unlike many other programming languages, **variables are untyped**. That means that if you do not explicitly declare its type, the content of a variable shall be arbitrarily a numeric value or a character string depending on which way you use it in.

***

#### <a name="part1"></a>1. Declaration

Declare a variable by simply typing its name immediatly followed by the operator `=`:

```shell
MY_VAR=
```

Fill its content by happening a value immediatly after the operator:

```shell
MY_VAR=42
MY_VAR="42"
MY_VAR="Forty two"
MY_VAR='Forty two'
```

These commands are similars. They assign a sequence of characters to the variable.
For greater clarity, we commonly do not use quotes or double-quotes when we want the variable to contain a numeric value, as in the first case.

Beware about spaces when declaring variables! 
The following commands will result in error:

```shell
MY_VAR = Something          # No spaces around the operator
MY_VAR=Something else       # Enclose character strings by quotes or double quotes
MY_VAR="Quotes are "life""  # Protect double quotes by adding a backslash like \"
```

If you want a variable to be typed, use the builtin command **declare** and its options:

```shell
declare -i MY_VAR           # Integer
declare -r MY_VAR           # Read-only
declare -i -r MY_VAR        # Read-only integer
declare -a MY_VAR           # Array
declare -f MY_VAR           # Function
```

Now we have declared a variable named MY_VAR and filled its content. This variable is only available in the environment of the current script. That means that when the script is terminated, its assigned value is destroyed. In order to export a variable outside of the environment of the script, we can use the following option `declare -x MY_VAR`.

***

#### <a name="part2"></a>2. Substitution

To manipulate an assigned value of a variable, use the special character `$` followed by the variable name:

```shell
MY_VAR="42FileChecker"
echo "The name of the script is $MY_VAR"
```

These commands display the value of the variable on stdout. We call it a **substitution** (or expansion) because Bash replaces the sequence $MY_VAR by the pointed value "42FileChecker" before executing the command line.

Depending on the context, it is sometimes necessary to use a less ambiguous syntax as you can see in the following example:

```shell
echo "script_name_$MY_VAR_rocks"
echo "script_name_${MY_VAR}_rocks"
```

Second line with braces **{** and **}** is safer because it differenciates the variable MY_VAR and a possible other existing variable named MY_VAR_rocks.

You can use a set of **special symbols** to make some special substitutions:

```shell
MY_VAR="42FileChecker"
MY_SECOND_VAR="MY_VAR"

echo "${#MY_VAR}"        # string length
                         # --> returns 13

echo "${MY_VAR:2:4}"     # offset and length, keep 4 chars starting at 2
                         # --> returns "File"

echo "${!MY_SECOND_VAR}" # substitute by referenced name
                         # --> interpreted as ${MY_VAR}
                         # --> returns "42FileChecker"

declare -a MY_ARRAY=(val1 val2 val3)

echo "${MY_ARRAY[@]}"    # values of the array 
                         # --> returns "val1 val2 val3"

echo "${!MY_ARRAY[@]}"   # indexes of the array 
                         # --> returns "0 1 2"
```

42FileChecker often use the substitution by referenced name, especially with array typed variables:

```shell
declare -a MY_ARRAY=(val1 val2 val3)    
MY_VAR="MY_ARRAY[1]"

echo "${!MY_VAR}"        # displays "val2"
```

MY_ARRAY is an array filled with 3 elements. MY_VAR stores a reference to the second element of the array MY_ARRAY. Finally, the symbol **!** indicates that we want to display a substitution of the reference $MY_VAR that is equivalent to $MY_ARRAY[1].

***

#### <a name="part3"></a>3. Positional and special parameters

A shell script or a shell function shall be invoked with a list of arguments. We call it the **positional parameters** which are similar to the pointer to strings 'argv' in a C program. A positional parameter is like a variable whose name is a number other than 0. Calling your script with the command `./my_script "arg1" "arg2"` will declare two positional parameters `1` and `2` with the assigned value "arg1" and "arg2":

```shell
echo "$1"    # displays the first argument --> "arg1"
echo "$2"    # displays the second argument --> "arg2"
echo "${14}" # add braces if the number contains more than one digit
```

You can shift positional parameters with the command `shift`, as well the following command lines are similar to the preceding:

```shell
echo "$1"    # displays the first argument
shift 1
echo "$1"    # displays the second argument
shift 12
echo "$1"    # displays the 14th argument
```

A list of **special paramaters** as `@`, `*` and `#` can help you managing the positional parameters. The paramater `#` contains the count number of arguments and the paramaters `@` and `*` expand the entire list of arguments, with a little difference as you can see in the following sequence:

```shell
echo "You called me with $# argument(s) wich are displayed bellow:"
echo "$*"

declare -a ARRAY1=("$@") # expands the arguments in separated fields
declare -a ARRAY2=("$*") # expands the arguments in a single field

echo "ARRAY1 now contained $# fields"
echo "ARRAY2 now contained 1 single field"
```

The special parameter `!` is often used in 42FileChecker, it contains the decimal process ID of the most recent background command executed by the script itself:

```shell
./ft_ls -Rl / &   # this command is sent to background with the symbol '&'
echo "PID of the last background command: $!"
```