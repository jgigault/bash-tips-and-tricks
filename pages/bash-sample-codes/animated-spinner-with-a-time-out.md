# Animated spinner with a time out

Display an animated spinner until a background process has terminated and check for a time out, like 42FileChecker does.

***

### <a name="code"></a>Sample code

Credits for this tip: [Bash Spinner](http://fitnr.com/showing-a-bash-spinner.html) (Author: LOUIS MARASCIO) to which I added the time-out feature.


```bash
function display_spinner
{
    local PID=$1             # 1st argument: PID of the background process
    local TIMEOUT=$2         # 2st argument: Time out in seconds

    local DELAY=0.75         # Delay for each frame of the animation
    local FRAMES='|/-\'      # Each symbol is a frame of the animation

    # Calculate the maximum number of loops (time out)
    local -i MAX_LOOP
    MAX_LOOP=$(awk -v t=$TIMEOUT -v d=$DELAY 'BEGIN {printf "%d", t/d}')

    # Start the loop until the background process $PID has terminated
    while [ ! -z "$(ps a | awk '{print $1}' | grep $PID)" ];
    do
        # If the time out has been reached:
        # kill the process $PID and exit function
        if [ "$MAX_LOOP" -le 0 ]
        then
            kill $PID            # Call the builtin kill
            wait $! 2>/dev/null  # Wait for the killing process
            return               # Exit the function
        fi

        # Display the current frame and the time out
        printf " [%c] %-4d " "$FRAMES" "$MAX_LOOP"

        # Move the current frame at the end of $FRAMES
        local TMP=${FRAMES#?}
        FRAMES=$TMP${FRAMES%"$TMP"}

        # Wait during the delay and erase the current frame
        sleep $DELAY
        printf "\b\b\b\b\b\b\b\b\b\b"

        # Decrement the time out
        (( MAX_LOOP -= 1 ))

    done
}

(sleep 10) &           # Send a process to the background with '&'
                       # e.g.: (ft_ls -R /) &

display_spinner $! 60  # Call the spinner animation with:
                       # - 1st argument: the last PID
                       # - 2nd argument: time out in seconds
```

***

### Tips

***

##### <a name="tip1"></a>Arithmetic operation with floating numbers

Bash programming does not allow to manipulate floating numbers. When you assign a floating number to a variable, the Shell interprets this variable as a common string:

```bash
declare FLOAT=0.75        # These two commands are similar
declare FLOAT="0.75"

declare -i FLOAT=0.75     # This command results in syntax error!
                          # 0.75 is not an integer
```

Doing an arithmetic operation with floating numbers and keep the precision can be done with an `awk` script (Note: Avoid the calculator `bc` because it is not available in all Unix distributions). The idea is first to pass our two floating numbers to the awk script with the option `-v`:

```bash
# Print 'n1' at the beginning of the script
awk -v n1=60 'BEGIN {print n1}'

# Print 'n1' and 'n2' at the beginning of the script
awk -v n1=60 -v n2=0.75 'BEGIN {print n1, n2}'
```

Now we have `n1` and `n2` declared in the awk script, we can use `printf` and its float format `f` with precision and save the result in a variable $RESULT:

```bash
declare N1=60
declare N2=0.75
declare RESULT

RESULT=$(awk -v n1=$N1 -v n2=$N2 'BEGIN {printf "%.2f", n1/n2}')  # Divide
RESULT=$(awk -v n1=$N1 -v n2=$N2 'BEGIN {printf "%.2f", n1*n2}')  # Multiply
RESULT=$(awk -v n1=$N1 -v n2=$N2 'BEGIN {printf "%.2f", n1+n2}')  # Add
RESULT=$(awk -v n1=$N1 -v n2=$N2 'BEGIN {printf "%.2f", n1-n2}')  # Substract
```

***

##### <a name="tip2"></a>Check if a process ID has terminated

The first idea is to send a process to the background by using the symbol `&` at the end of the command line, and then to do something else until it has terminated. You must send the process to the background otherwise you won't be able to do anything:

```bash
ft_ls -R /            # This process is executed normally
echo "This line is printed after the preceding command has terminated"

(ft_ls -R /) &        # This process is sent to background
echo "The PID of the background process is: $!"
echo "The script does immediately continue its execution"
echo "This line appears immediately!"
```

The `ps` utility (process status) enables you to see the list of processes that are currently running. If you know the PID (process ID) of a process, you can parse the `ps` result and check if it is still running, as following:

```bash
# Print the list of running processes
ps a                 

# Scan each line with 'awk' and print only the first column (PID)
ps a | awk '{print $1}'

# Look for a PID with 'grep' and print it
ps a | awk '{print $1}' | grep $PID
```

When the result is an empty string, that means the process $PID is not running and has terminated. Use this command in a loop and test if it is an empty string by using the zero test `-z`:

```bash
(ft_ls -R /) &                   # Send a process to background
declare PID=$!                   # And save its ID in a variable

# Start a loop while $PID is found in the list of process status
# '-z' is success if the string is empty, use '!' to inverse the status
while [ ! -z "$(ps a | awk '{print $1}' | grep $PID)" ];
do
    echo "$PID is still running"
    wait 0.75                    # Wait 0.75 seconds and re-loop
done
echo "$PID has terminated"

```

***

##### <a name="tip3"></a>Move the first character to the end of a string

Bash programming offers you a multitude of parameters for manipulating and/or expanding variables. Use a **removal parameter** `#` or `%` followed by a pattern for making a removing:
* #\<pattern> removes the shortest match from the beginning
* ##\<pattern> removes the longest match from the beginning
* %\<pattern> removes the shortest match from the end
* %%\<pattern> removes the longest match from the end

The symbol `?` inside a pattern means "Any one character", so that the substitution `#?` means "Remove one character from the beginning of the string":

```bash
declare MY_STRING="ABCDEF"
echo $MY_STRING                    # Print "ABCDEF"

declare TMP=${MY_STRING#?}
echo $TMP                          # Print "BCDEF"
```

Now, if you use the pattern $TMP with the removing parameter `%` applied to the string $MY_STRING, that means you want to remove the string "BCDEF" from the end of the string "ABCDEF", resulting in the character "A":

```bash
echo ${MY_STRING%"$TMP"}           # Print "A"
```

Finally, concatenate $TMP and ${MY_STRING%"$TMP"} results in moving the first character of $MY_STRING to the end of the string:

```bash
MY_STRING=$TMP${MY_STRING%"$TMP"}
echo $MY_STRING                    # Print "BCDEFA"
```