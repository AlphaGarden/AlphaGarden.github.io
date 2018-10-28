---
layout: single
title:  "Bash Notes"
header:
  overlay_color: "#000"
  overlay_filter: "0.1"
  overlay_image: /
  cta_label: "Learn stuff step by step"
  cta_url: "https://github.com/AlphaGarden"
  caption: "Photo credit: [**_Jiayong Mo_**](https://alphagarden.github.io)"
classes: wide
date:   2018-06-23 16:26:01 -0600

excerpt: "A personal notes for Bash learning"
categories: Bash Shell Script
---

In Bash, 0 means everything goes well, non 0 means error happens

```bash
#!/bin/bash
```

```
#!interpreter [optional-arg]
```

In a `Unix-like` operating systems, when a text file with a shebang is used as if it is an executable, the program loader parses the rest of the file's initial line as a `interpreter directive`.

#### 0. Data Type in bash

Basically, there are no data types in bash, a bash variable can contain a number, a character, a string of characters.  And basically a variable declared in bash has global scope, but sometimes we want it only to be  seen in the function, then we can use the keyword `local` before assigning	the value to variable. 

Just like the below example. 

```bash 
#!/bin/bash
HELLO=Hello 
function hello {
        local HELLO=World
        echo $HELLO
}
echo $HELLO (Hello)
hello       (World)
echo $HELLO (Hello)
```

In bash, there is a kind of data type called array.  Array in bash have the following features:

* There is no maximum limit to the size of an array
* There is no any requirement that member variables should be indexed or assigned continuously. 

```bash
# Option 1, assigned a value VALUE to an array under index INDEX
ARRAY[INDEX]=VALUE
# Option 2, declare an array explicitly
declare -a ARRAYNAME
# Option 3, using compound assignments
# each VALUE follow the pattern [indexnum=]string 
# If indexnum is supplied, then assign the num to that index
# Otherwise, the index of element assigned is the number of the last index that was assigned + 1.
# If no index numbers are supplied, index starts at zero.
ARRAY=(VALUE1 VALUE2 ... VALUEn)  
```

In order to `access` the value in the array, we need to use curly brace. If index number of `@` or `*`, then all numbers of an array are referenced.

```bash
# Assume ARRAY=(1 2 3)
echo ${ARRAY[0]}
1
echo ${ARRAY[*]}
1 2 3
echo ${ARRAY[@]}
1 2 3

# Add an element into array
ARRAY[3]=4

echo ${ARRAY[*]}
1 2 3 4
```

To `delete` an element we can use `unset` built-in to destroy arrays or members of an array

```bash
# Assume ARRAY=(1 2 3)
unset ARRAY[1]

echo ${ARRAY[*]}
2 3

unset ARRAY

echo ${ARRAY[*]}
<--no output-->
```


#### 1. Simple Command 

`Pipeline`
A pipeline is a sequence of one or more commands separated by one of the control operators `|`  or `|&`.
| The output of each command in the pipeline is connected via a pipe to the input of the next command.
|& The standard error of the previous command in addiction to its standard output, is connected to the standard input of the next command

`;`
semicolon


`&`
And command 
If a command is terminated by the control operator &, the shell executes the command asynchronously in the subshell, which is known an executing the command in the background. 
`||` 
Double OR command
`command1 || command2`
command2 is executed if and only if, command1 returns a non-zero exit status. In another words, the command2 will be executed if and only if command1 encounters an error. 

`&&`
Double AND command
`command1 && command2`
command2 is executed id and only if, command1 returns a exit status of zero. In another words, the command2 will be executed if and only if command1 successfully is executed.

#### 2. Compound commands
 * [Looping Constructs](https://tiswww.case.edu/php/chet/bash/bashref.html#Looping-Constructs)
 * [Conditional Constructs](https://tiswww.case.edu/php/chet/bash/bashref.html#Conditional-Constructs)
 * [Command Grouping](https://tiswww.case.edu/php/chet/bash/bashref.html#Command-Grouping)

##### 2.1 Looping Constructs
`until`,  `while`, `for`

-----------

`until`
``` bash
until test-commands 
	do consequent-commands
done
```
```bash
i=1
until [ $i -gt 6 ]
do
	echo "Welcome $i times."
	i=$(( i+1 ))
done
```

Execute consequent-commands as long as test-commands has an exit status of zero. The return status is the exit status of the last command executed in consequent-commands, or zero if none was executed.

`while`
``` bash
while test-commands
	do consequent-commands
done
```
```bash
#IFS is used to set field separator (default is while space). The -r option to read command disables backslash escaping (e.g., \n, \t). This is failsafe while read loop for reading text files.
while IFS= read -r field1 filed2 field3 ... fieldN
           do
                 command1 on $field1
                 command2 on $field1 and $field3
                 ..
                 ....
                 commandN on $field1 ... $fieldN
           done < "/path/to dir/file name with space"
```

Execute consequent-commands as long as test-commands has an exit status of zero. The return status is the exit status of the last command executed in consequent-commands, or zero if none was executed.

`for`
``` bash
for name [[in [words ...]]]
	do commands
done
```
```bash
#!/bin/bash
# A shell script to verify user password database
files="/etc/passwd /etc/group /etc/shadow /etc/gshdow"
for f in $files
do
	[  -f $f ] && echo "$f file found" || echo "*** Error - $f file missing."
done

# A simple shell script to display a file on screen passed as command line argument
[ $# -eq 0 ] && { echo "Usage: $0 file1 file2 fileN"; exit 1; }

# read all command line arguments via the for loop
for f in $*
   do
   echo
   echo "< $f >"
   [ -f $f ] && cat $f || echo "$f not file."
   echo "------------------------------------------------"
done
```

Expand words, and execute commands once for each member in the resultant list, with name bound to the current member.

``` bash
for ((expr1 ; expr2 ; expr3)) 
	do commands
done
```
This is command can be known as nested loop, where the total time of running will be `expr1 * expr2 * expr3`

--------
##### 2.2 Conditional Constructs
`if`, `case`, `select`, `((...))`, `[[...]]`

--------
``` bash
if test-commands ; then
	consequent-commands;
[elif more-test-commands; then
	more-consequent-commands;]
[else 
	alternate-consequent-commands;]
fi
```
```bash
# Check if $FILE exists or not
if test ! -f "$FILE" 
then   
	echo "Error - $FILE not found or mcelog is not configured for 64 bit Linux systems."
	exit 1
fi

#!/bin/bash
read -p "Enter a number : " n
if [ $n -gt 0 ]; then
  echo "$n is a positive."
elif [ $n -lt 0 ]
then
  echo "$n is a negative."
elif [ $n -eq 0 ]
then
  echo "$n is zero number."
else
  echo "Oops! $n is not a number."
fi
```

This is a very similar pattern as if-else of C language 

-------

`case`
The basic pattern for case command in the bash shell
``` bash
case word in 
	[[(] pattern [| pattern]...)
		command-list ;;]
	...
esac
```
```bash
case $space in
[1-6]*)
  Message="All is quiet."
  ;;
[7-8]*)
  Message="Start thinking about cleaning out some stuff.  There's a partition that is $space % full."
  ;;
9[1-8])
  Message="Better hurry with that new disk...  One partition is $space % full."
  ;;
99)
  Message="I'm drowning here!  There's a partition at $space %!"
  ;;
*)
  Message="I seem to be running with an nonexistent amount of disk space..."
  ;;
esac
```

 a list of pattern and an associated command-list is known as a ***clause***, and `)` operator terminates a pattern list.

``` bash
[[(] pattern [| pattern]...)
		command-list ;;]
```
* If the `;;` operator is used, no subsequent matches are attempted after the first pattern match. 
* Using `;&` in place of `;;` causes execution to continue with the next ***clause***, if any. 
* Using `;;&` in place of `;;` causes the shell to test the patterns in the next ***clause*** and execute the associated command-list on a successful matched.

------

A straight-forward example for case command
``` bash
read ANIMAL
case $ANIMAL in 
	(horse | dog | cat | pig)
		echo -n "four legs";;
	(man | kangaroo)
		echo -n "two legs";;
	(*)
		echo -n "an unknown number of legs";;
esac
```

-------

`select`

`select WORD [in LIST]; do RESPECTIVE-COMMANDS; done`

```bash
PS3='Please enter your choice: '
options=("Option 1" "Option 2" "Option 3" "Quit")
select opt in "${options[@]}"
do
    case $opt in
        "Option 1")
            echo "you chose choice 1"
            ;;
        "Option 2")
            echo "you chose choice 2"
            ;;
        "Option 3")
            echo "you chose choice $REPLY which is $opt"
            ;;
        "Quit")
            break
            ;;
        *) echo "invalid option $REPLY";;
    esac
done

```

`((...))`

``` bash
(( expression ))
```
If the value of the expression is non-zero,  the return status is 0;
otherwise the return status is 1.
`[[...]]`

``` bash
[[ expression ]]
```
Return a status of 0 or 1 depending on the evaluation of the conditional expression. 



#### 3 Shell Function
``` bash
name() compound-command [ redirections]
or
function name [()] compound-command [ redirections ]
```
* The compound command is usually a list enclosed between `{ `and `}`

#### 4 Shell Parameters
* [Positional Parameters](https://tiswww.case.edu/php/chet/bash/bashref.html#Positional-Parameters)
* [Special Parameters](https://tiswww.case.edu/php/chet/bash/bashref.html#Special-Parameters)

```bash
${parameter:-default word}
    # If parameter is unset or null, the expansion of word is substituted.
    # Otherwise, the value of parameter is substituded. 
```
#### 5 Executable commands
##### 5.1 `sed`, a stream editor 

```bash
sed SCRIPT INPUTFILE ... # If the INPUTFILE is -, sed accepts the standard input
```

* Command-Line Option
    *  `-e script, --expression=script` Add the commands in script to the set of commands to be run while processing the input.
    * `-i[SUFFIX] --in-place[=SUFFIX]` This option specifies that files are to be edited in-place. GNU does this by creating a temporary file and sending output to this file rather than to standard output.

* The `s` command is probably `the most important` in `sed` and has a lot of different options. The syntax of the `s` command is *`s/regexp/replacement/flags`*.

* *replacement* The replacement can contain `\` references. which refer to the portion of the match which is contained between the n*th* \ . 

* *regexp* [see regular expression][regular_expression_url]

* *flags*
    * `g` Apply the replacement to *all* matches to the *regexp*, not just the first.

    * `number` Only replace the *numberth* match of the *regexp*.

    * `p` If the substitution was made, then print the new pattern space.

    * `w filename` If the subsititution was made, then write out the result to the named file.

    * `I`,`i` makes sed match *regexp* in a case-insensitive manner.

    * `M`,`m` causes ^ and $ to match respectively the emty string after a newline, and the emtry string before a new line.  

      egular_expression_url]:https://www.gnu.org/software/sed/manual/sed.html#BRE-syntax	"BRE"

* *Multiple commands syntax*

    * Using new line when running a *sed* script from a file (using the -f option)

        * ```bash
          $ seq 6 | sed '1d
                         2d
                         5d'
          2
          4
          6
          ```
    * A semicolon ( `;` ) can be use to separate the simple commands

        * `seq 6 | sed '1d; 3d; 5d'`

##### 5.2 `cut` cut out selected portions of each line of a file 

```bash
cut -e d = -f 2 - # cut the stand input by delimiter by '=' and select the 2nd portion. 
```

##### 5.3 `read` Read a line from the standard input and split it into fields.

```bash
echo "hello world" | read -r line 
```

#### 6 FAQ

##### 6.1 How to read from standard input line by line? 

First of all we should know what is `$IFS` variable in linux bash, `$IFS` (Internal ) is kind of special vairiable in bash and usually used in `read bultetin command` and we should know about the operator `<`  which is mots commonly used to redirect the file contents.

The shell treats each character of IFS as a delimiter, and splits the results of the other expansions into words on these characters. If IFS is unset, or its value is exactly `<space><tab><newline>`, the 

we put the following string int he file `/tmp/text.txt`

```wiki
cyberciti.biz|202.54.1.1|/home/httpd|ftpcbzuser
nixcraft.com|202.54.1.2|/home/httpd|ftpnixuser
```

```bash
#!/bin/bash
# setupapachevhost.sh - Apache webhosting automation demo script
file=/tmp/text.txt

# set the Internal Field Separator to |
IFS='|'
while read -r domain ip webroot ftpusername
do
        printf "*** Adding %s to httpd.conf...\n" $domain
        printf "Setting virtual host using %s ip...\n" $ip
        printf "DocumentRoot is set to %s\n" $webroot
        printf "Adding ftp access for %s using %s ftp account...\n\n" $domain $ftpusername
	
done < "$file"
```



##### 6.2 What are difference between backtick/backquote  ` `` ` and `$()` in bash?

Bascially,  backquote and `$()` almost serve the same purpose of putting the output after executing the command inside it, then the back quote is quite more original command, and this kind of syntax is kind of confusing comparing to single quote.  And bascially, there some other kind of usecases like `$(command)`,`${vairiable}`,`$((expression))`.