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

#### Bash notes

In Bash, 0 means everything goes well, non 0 means error happens


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
Execute consequent-commands as long as test-commands has an exit status of zero. The return status is the exit status of the last command executed in consequent-commands, or zero if none was executed.

`while`
``` bash
while test-commands
	do consequent-commands
done
```
Execute consequent-commands as long as test-commands has an exit status of zero. The return status is the exit status of the last command executed in consequent-commands, or zero if none was executed.

`for`
``` bash
for name [[in [words ...]]]
	do commands
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


component-tag
