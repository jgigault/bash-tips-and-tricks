# Awk

AWK is an interpreted programming language designed for text processing and typically used as a data extraction and reporting tool. It is a standard feature of most Unix-like operating systems. (Source: [Wikipedia](https://en.wikipedia.org/wiki/AWK))

***

#### <a name="part1"></a>1. How to use AWK

An awk script can be used in several ways. It is commonly used right inside a command-line as following:

```bash
cat "path/to/a/file/to/scan" | awk '<AWK SCRIPT>' > ./output_file
```

The same result by placing the file path as an argument:

```bash
awk '<AWK SCRIPT>' "path/to/a/file/to/scan" > ./output_file
```

When an awk script becomes complexe and long, put it in a file (e.g. AWK_SCRIPT.awk) and call `awk` with the option `-f`:

```bash
cat "path/to/a/file/to/scan" | awk -f AWK_SCRIPT.awk > ./output_file
```
or
```bash
awk -f AWK_SCRIPT.awk "path/to/a/file/to/scan" > ./output_file
```

Finally, you can add a [shebang](https://en.wikipedia.org/wiki/Shebang_(Unix)) at the beginning of the file on the very first line (`#!/bin/awk -f`) and use the your script (e.g. AWK_SCRIPT.awk) as a binary:

```bash
cat "path/to/a/file/to/scan" | ./AWK_SCRIPT.awk > ./output_file
```
or
```bash
./AWK_SCRIPT.awk "path/to/a/file/to/scan" > ./output_file
```

And the file AWK_SCRIPT.awk should look like this:

```awk
#!/bin/awk -f
<AWK SCRIPT>
```

***

#### <a name="part2"></a>2. How works AWK

Awk reads a file descriptor one line at a time. For each line, called **records**, awk executes one or more blocks of instructions surrounded by braces `{...}`. The most basic instruction is `print`:

```awk
{print}               # Print each line on the standard output
```

Awk splits each **record** into **fields**, or columns, according to a character (the character `<space>` by default). Each **field** is stored in a positional parameter $1, $2, $3.. where $1 is the first field:

```awk
{print $1, $2, $3}    # Print the first three fields of each line
                      # If a line is:    "This is a line from a file"
                      # Output would be: "This is a"
```

The positional parameter `$0` contains the entire line, as the following instructions are similar:

```awk
{print}               # Print each line on the standard output
{print $0}
```

Add several instructions by separating them with the symbol `;`:

```awk
{print $0; print $0} # Print each line twice on the standard output

```

Awk may execute several blocks of instructions for each line:

```awk
{print $0} {print $0} # Print each line twice on the standard output
```

A block of instructions may have a condition which is prefixed:

```awk
$0 == "this is a string" {print $0}
# Print each line that is strictly equal to "this is a string"

$0 ~ /this is a string/ {print $0}
# Print each line that contains the string "this is a string"
```

Several blocks of instructions may have their own condition:

```awk
$0 ~ /42/ {print $0} $0 ~ /42 School/ {print "WOW"} 
# Print lines that contain the term '42'
# And then print 'WOW' if the same line contain the term '42 School'
```

***

#### <a name="part3"></a>3. AWK built-in variables

_To be written..._

***

#### <a name="part4"></a>4. BEGIN and END statements

An awk script may have a BEGIN and an END statements. These blocks of instructions are executed before or after reading the file. Use it to declare local variables or to display headings/footers:

```awk
BEGIN {print "START"}  {print $0}  END {print "END OF FILE"}
# Will output:  START
#               [Lines of the file]
#               END OF FILE
```

_To be written..._

***

#### <a name="part5"></a>5. Simple AWK examples

For greater readability in the following examples, the file is first read into a variable "$TEXT" which is sent within the standard entry to awk:

```bash
declare TEXT=$(cat "./path/to/the/file")

echo "$TEXT" | awk '$0 ~ /word/ {print $0}'
# Print lines that contain the term 'word'

echo "$TEXT" | awk '$0 ~ /^word/ {print $0}'
# Print lines that begin with the term 'word'

echo "$TEXT" | awk '$0 ~ /word$/ {print $0}'
# Print lines that end with the term 'word'

echo "$TEXT" | awk '$0 ~ /^word$/ {print $0}'
# Print lines that strictly contain the term 'word'

echo "$TEXT" | awk '$0 ~ /word\t/ {print $0}'
# Print lines that contain the term 'word' followed by a tab

echo "$TEXT" | awk '$0 ~ /[\t]word/ {print $0}'
# Print lines that contain the term 'word' prefixed by a tab

echo "$TEXT" | awk '$0 ~ /word[\t]?/ {print $0}'
# Print lines that contain the term 'word' followed by zero or one tab

echo "$TEXT" | awk '$0 ~ /word[\t]+/ {print $0}'
# Print lines that contain the term 'word' followed by one or more tabs

echo "$TEXT" | awk '$0 ~ /word[\t]*/ {print $0}'
# Print lines that contain the term 'word' followed by 
# zero, one or more tabs

echo "$TEXT" | awk '$0 ~ /word[\t]{1,2,3}/ {print $0}'
# Print lines that contain the term 'word' followed by 
# one, two, or three tabs

echo "$TEXT" | awk '$0 ~ /word[\t ]/ {print $0}'
# Print lines that contain the term 'word' followed by
# either a space or a tab

echo "$TEXT" | awk 'BEGIN {print "I found this:"} $0 ~ /word/ {print $0}'
# At the beginning of the script, print the sentence "If found this:"
# Then find and print lines that contain the term 'word'

echo "$TEXT" | awk '$0 ~ /word/ {print $0} END {print "I have read all."}'
# Print lines that contain the term 'word'
# At the end of the script, print the sentence "I have read lines."

echo "$TEXT" | awk '$0 ~ /word/ {print "true"; exit} END {print "false"}'
# Find a line that contains the term 'word', print "true" and
# stop searching by exiting the script.
# If no line is found, the 'END' block is executed and prints "false"
```

***

#### <a name="part6"></a>6. A complete AWK example

_To be written..._