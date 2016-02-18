# Functions

Functions enable you to structure your code, to increase readability and to avoid repetition of lines of code.

***

#### <a name="part1"></a>1. Syntax of a function

Declare a function by using the keyword `function` followed by its name:

```bash
function name_of_function
{
    # Lines of code here
}

name_of_function   # Call the function
```

To simply call the function, put its name at the beginning of a command line. It will cause the code between the two embraces `{ ... }` to be executed. 

As a C program, functions may return a numeric value called **exit status** by using the keyword `return`. When `return` is encountered, the execution of the function is stopped and the immediate value after `return` is returned:

```bash
function return_one
{
    # Lines of code executed
    return 1
    # Lines of code NEVER executed
}

return_one         # Call the function
echo $?            # Print the exit status of the last command
```

***

#### <a name="part2"></a>2. Local and global variables

When you declare a variable, wherever in a script and even in a function, its reference is globally accessible. To specify a variable to be local only, use the keyword `local`:

```bash
GLOBAL_VAR="Hello"
function display_global_and_local
{
    local LOCAL_VAR="World"
    echo "${GLOBAL_VAR} ${LOCAL_VAR}"
}

display_global_and_local            # Print "Hello World"
echo "${GLOBAL_VAR} ${LOCAL_VAR}"   # Print "Hello "
```

***

#### <a name="part3"></a>3. Arguments of a function

The arguments of a function are not explicitly declared. As you read it in the chapter "Bash syntax: Variables", the arguments of a script or a function are called **positional parameters**. They are listed and named like this: `$1`, `$2`, `$3`...

Passing a list of arguments to a function `display_text` will cause the positional parameters to be declared:

```bash
function display_text
{
    echo "$1"
}

display_text "This is the first argument"
```

`$0` is not a positional parameter, it always contains the name of the script that is currently executed.

***

#### <a name="part4"></a>4. Passing an array as argument

Arguments can only be text like in C programming and the array `**argv`. The tip consists in passing only the name of the array as an argument, declaring a local one and filling it by using a substitution by referenced name.

```bash
function display_array
{
    OLD_IFS=$IFS
    IFS=''
    local ref="$1[*]"
    local -a MY_LOCAL_ARRAY=(${!ref})     # Expand the array with the reference
    IFS=$OLD_IFS

    echo $MY_LOCAL_ARRAY[0]
    echo $MY_LOCAL_ARRAY[1]
    echo $MY_LOCAL_ARRAY[2]
}

declare -a MY_ARRAY=("First item" "Second item" "Third item")
display_array MY_ARRAY                    # Call the function with the name of the array
```

Firstly, declare a temporary variable `ref` that will contain the name of the array to expand. Assigning the value `$1` will cause the variable `ref` to contain the first argument:

```bash
local ref="$1"
echo ${ref}            # Print "MY_ARRAY"
```

Then, add the symbol `!` to tell the Shell that we want to print the values of the array rather than its name:

```bash
local ref="$1"
echo ${ref}            # Print "MY_ARRAY"
echo ${!ref}           # Print the first value of MY_ARRAY: "First item"
```

As you can see, `$(!ref)` becomes an equivalent to `$(MY_ARRAY)`, that is also equivalent to `$(MY_ARRAY[0])` (The first value of the array). Adding the symbols `[*]` tells the Shell that we are looking for all the values of the array:

```bash
local ref="$1[*]"
echo ${ref}            # Print "MY_ARRAY[*]"
echo ${!ref}           # Print all the values of MY_ARRAY: "First item Second item Third item"
```

Now we can assign the values in a local array for a simplier use:

```bash
local ref="$1[*]"
local -a MY_LOCAL_ARRAY=(${!ref})  # Declare an array and fill it with the values

echo $MY_LOCAL_ARRAY[0]            # Print the 1st value: "First" 
echo $MY_LOCAL_ARRAY[1]            # Print the 2nd value: "item"
echo $MY_LOCAL_ARRAY[2]            # Print the 3rd value: "Second"
```

Damn! We got a new array `MY_LOCAL_ARRAY` that contains 6 items instead of 3! The Shell expanded the array `MY_ARRAY` word by word each in a row. 

To resolve the problem, we need to understand how the symbols `[*]` are interpreted: The Shell is looking for the first character contained in the global variable `IFS` (Internal Field Separator) and does split the content of the array with it, without taking care about the structure of the array. Default value of `IFS` is `<space><tabulation><newline>` so that the array is expanded like this:

```bash
local -a MY_LOCAL_ARRAY=("First" "item" "Second" "item" "Third" "item")
```

Remove the value of `IFS` before expanding the array to save its structure, as following: 

```bash
OLD_IFS=$IFS                       # Save the current value of IFS
IFS=''                             # Remove the value of IFS
local ref="$1[*]"
local -a MY_LOCAL_ARRAY=(${!ref})
IFS=$OLD_IFS                       # Restore the old value of IFS

echo $MY_LOCAL_ARRAY[0]            # Print the 1st value: "First item" 
echo $MY_LOCAL_ARRAY[1]            # Print the 2nd value: "Second item"
echo $MY_LOCAL_ARRAY[2]            # Print the 3rd value: "Thirs item"
```

`MY_LOCAL_ARRAY` now contains the same values as MY_ARRAY :-)