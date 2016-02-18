# Conditions

Sometimes you want a command to be executed or not...

***

#### <a name="part1"></a>1. Conditional expressions

A conditional expression si similar to an Yes-no question in the real life, it has to return a boolean value (true or false). In bash programming language the value of true is `0` (that means `success`) and false is a non-zero value.

You can use, as a conditional expression, every commands that return an **exit status** that can be interpreted as a boolean. 

For example: In C programming language, the exit status is the value returned by the function `main()`. If we omit the standard and error outputs of a C program, like the project `ft_ls` made at 42 school, we can use its exit status as a conditionnal expression because it should return `0` when its execution is successful, and a non-zero value when an error occurs.

````shell
./ft_ls -Rl / &>/dev/null   # '&/dev/null' suppress outputs (standard and error)
                            # returns 0 if success -> true
                            # returns non-zero     -> false
````

In bash scripts, the builtin command `test` is commonly used in conditional expressions. It enables you
to make comparison expressions and file tests:

````shell
test -f "filename"          # true if the file exists
test -d "dirname"           # true if the directory exists
test -n "$MY_VAR"           # true if the value of MY_VAR is not null
test "$MY_VAR" == "Yes"     # true if the value of MY_VAR is equal to "Yes"
test "$MY_VAR" -eq 1        # true if the arithmetic value of MY_VAR is equal to 1
test "$EXAM_RATE" -ge 75    # true if the arithmetic value of EXAM_RATE is greater than or equal to 75
````

Use the alias `[` of the builtin command `test` for a simpler code (Newer versions of Bash require the closing tag `]`). These are the equivalents of the preceding commands:

````shell
[ -f "filename" ]
[ -d "dirname" ]
[ -n "$MY_VAR" ]
[ "$MY_VAR" == "Yes" ]
[ "$MY_VAR" -eq 1 ]
[ "$EXAM_RATE" -ge 75 ]
````

Arithmetic comparisons can also be performed with a more familiar syntax (as in C language) thanks to the builtin command `let` or its equivalent `((...))` construct. These following commands are similar:

````shell
test "$EXAM_RATE" -ge 75
[ "$EXAM_RATE" -ge 75 ]
let "$EXAM_RATE >= 75"
(( "$EXAM_RATE" >= 75 ))
````

***

#### <a name="part2"></a>2. If-then-else

The `if-then-else` construct requires a conditional expression. If the result of the expression is true, the commands that follow the `then` are executed. Otherwise the script execution continues in the optional `else` block or, if it does not exist, continues after the keyword `fi` which ends the `if-then-else` construct:

````shell
if (( "$MY_EXAM_GRADE" >= 75 ))
then
    echo "I validated my exam"
else
    echo "I did not validate my exam"
fi
````

You can also use the keyword `elif` followed by a conditional expression when a single conditional expression does not handle all the possibilities you want:

````shell
if (( "$MY_EXAM_GRADE" < 75 ))
then
    echo "I did not validate my exam"
elif (( "$MY_EXAM_GRADE" <= 100 ))
    echo "I validated my exam"
else
    echo "I validated my exam with additional bonus"
fi
````

***

#### <a name="part3"></a>3. Case-in

Rather than creating a long sequence of `if` and `elif` when you want a single value to be compared with several patterns, use the `case-in` statement whose closing tag is `esac`: 

````shell
case "$PROJECT_NAME" in
    "libft")     echo "This is the libft project"         ;;
    "ft_ls")     echo "This is the ft_ls project"         ;;
    "ft_printf") echo "This is the ft_printf project"     ;;
    *)           echo "This is not a 42 school's project" ;;
esac
````

The value surrounded by the keywords `case` and `in` is compared with each pattern. If a pattern matches, the commands following the keyword `)` are executed until `;;` is encountered, then the program execution continues after the keyword `esac`. The symbol `*` is a wildcard that matches with all sequence of characters, even an empty string, allowing you to set a default statement when any pattern does match.