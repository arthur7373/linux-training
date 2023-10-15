# Shell programming in Linux


## Linux Terminal (Լինուքսի հրամանների տողը)

> Ներածական հարցեր թեմայի վերաբերյալ 
* Ծանո՞թ եք տերմինալի միջոցով Linux-ում աշխատելուն:
* Եթե այո ի՞նչ ծրագրի միջոցով:
* Ծանո՞թ եք SSH-ով միանալուն:


Լինուքս համակարգ մուտքագրվելու համար պետք է տրամադրել անունը (username) և գաղտնաբառը (password):

SSH-ով միանալու պարագայում գաղտնաբառի փոխարեն հնարավոր է մուտքը զույգ-բանալիներով (Public/Private Keys)

Մուտքագրվելիս Լինուքս համակարգ հնարավոր է աշխատել հետևյալ տարբերակներից մեկով. 
* Graphical User Interface (GUI)
* Command Line Interface (CLI)/Terminal

Այս դասընթացի ընթացքում մենք կաշխատենք երկրորդ տարբերակով:

<br><br>

### Linux Terminal, CLI Basics

> Լինուքսի յուրահատկությունը


* Մեծատառի/փոքրատառի տարբերություն (LiNuX iS CaSe SeNsItIvE)
  * Հրամանների և ծրագրերի անվանման մեջ 
    * Command 
    * COMMAND 
  * Ֆայլերի և դիրեկտորիաների անվանման մեջ
    * File
    * FILE
  * Օգտագործողների և խմբերի անվանման մեջ
    * user
    * User


> Command Prompt

**$** - User Prompt

**#** - ROOT Prompt

> Command History 
* Վերևի սլաքը (Up Arrow) նախորդ հրամանները

> Filename/Command completion 	
* [Tab]	հրամանի/ֆայլի լրացում
* [Tab] [Tab]	լրացում բոլոր տարբերակների ցուցադրում

> Movement:	
* Ctrl-A – mտեղափոխել տողի սկիզբ
* Ctrl-E – տեղափոխել տողի վերջ


> Հրամանների կառուցվածք

<img src=https://github.com/arthur7373/linux-training/blob/main/images/shell-course/command-structure.png width=50% height=50% >

> Հրամանների օրինակներ

* cal - display a calendar

  * `cal`
  * `cal 2 2015`
  * `cal -m 2 2015`

* date - display date

  * `date`
  * `date --help`
  * `date +"%d-%m-%Y"`
  
* echo - display a line of text 
  * `echo We learn shell`
  
* sleep - delay for a specified time 
  * `sleep 2 ; echo Linux rules`

<br><br>

### File Management Commands

> Ֆայլերի անվանումը
* Windows
  * `C:\Program Files\Oracle\VirtualBox\VirtualBox.exe`

* Linux/UNIX

  * `/home/user1/docs/letter.txt`
  * `/bin/ls`


> Հատուկ անվանումներ
* `/`   Գլխավոր դիրեկտորիան
* `.`    Տվյալ դիրեկտորիան
* `..`   Նախորդ (վերևի) դիրեկտորիան
* `~`    Օգտագործողի անձնական դիրեկտորիան

Օրինակներ՝

`./a`        նույնն է ինչ   `a`

`../home/student`  մեկ մակարդակ վերև և home/student

`.`-ով սկսվող ֆայլեր  

> Հրամանների օրինակներ
* `pwd` - ընթացիկ դիրեկտորիան
* `cd` - փոխել ընթացիկ դիրեկտորիան
* `touch` – ստեղծել դատարկ ֆայլ
* `ls` - ֆայլերի ցուցակ


`ls [options] <directory/file>`

* `-l`     ընլայնված ցուցակ
* `-a`  ցույց տալ բոլոր ֆալերը  (նեռարյալ .-ով սկսվող ֆալերը )
* `-S`   դասավորել ֆայլերը ըստ չափի (–lS)
* `-r`  Հակադարձ դասավորման կարգով (–lSr)
* `-h`    Մարդու համար ավելի ընթեռնելի (ֆայլերի չափը k, M, G-ով)


> Հրամանների օրինակներ

* `cd /bin`
* `pwd`
* `ls -la`
* `cd ~`
* `ls -la`
* `ls -la /bin`

* `cp -r /etc  ~`
* `mkdir ~/TEST`
* `mv  ~/etc  ~/TEST`
* `rm -r ~/TEST`


> Հրամաններ

* `cp <fromfile> <tofile>`   	Պատճենել ֆայլը 
  * cp –r
  
* `mv <fromfile> <tofile>`	Տեղափոխել / վերանվանել ֆայլը

* `rm <file>`  			Հեռացնել ֆայլ/դիրեկտորիա 
  * `rm –r`
  
* `mkdir <newdir>`		 Ստեղծել դիրեկտորիա 
* `alias <alias> <command>` Ստեղծել հրամանի կրճատում 
* `which <command>` 		 Հրամանի գտնվելու վայրը


> Հրամանների օրինակներ

* `cd ~`
* `pwd`
* `touch f1`
* `cp f1 f2`
* `mv f2  f3`
* `alias la='ls -la'`
* `la`

*`ls -l f*`
* `rm -i f*`
* `mkdir d1`
* `rm -r d1`

<br><br>

### File Permissions

<img src=https://github.com/arthur7373/linux-training/blob/main/images/shell-course/permissions.png width=50% height=50% >
<br><br>
<img src=https://github.com/arthur7373/linux-training/blob/main/images/shell-course/permissions2.png width=50% height=50% >
<br><br>
<img src=https://github.com/arthur7373/linux-training/blob/main/images/shell-course/permissions3.png width=50% height=50% >
<br><br>
<img src=https://github.com/arthur7373/linux-training/blob/main/images/shell-course/permissions4.png width=50% height=50% >
<br><br>
<img src=https://github.com/arthur7373/linux-training/blob/main/images/shell-course/chmod.png width=50% height=50% >
<br><br>
<img src=https://github.com/arthur7373/linux-training/blob/main/images/shell-course/mc.png width=50% height=50% >
<br><br>
<img src=https://github.com/arthur7373/linux-training/blob/main/images/shell-course/umask.png width=50% height=50% >


<br><br>

### I/O Redirection

<img src=https://github.com/arthur7373/linux-training/blob/main/images/shell-course/io-redir-1.jpg width=50% height=50% >
<br><br>
<img src=https://github.com/arthur7373/linux-training/blob/main/images/shell-course/io-redir-2.jpg width=50% height=50% >

> STDOUT - Standard output  		>     >> 
 
* `ls /etc > ~/stdout`
* `ls /etc >> ~/stdout`

> STDERR - Standard error output		2>   2>> 
* `ls /e > ~/stdout`
* `ls /e > ~/stdout 2> ~/stderr`
* `ls /e > ~/stdout 2> /dev/null`

<br><br>

### Pipes

Pipeline - Մեկ հրամանի STDOUT-ը ուղարկել այլ հրամանի STDIN-ին

<img src=https://github.com/arthur7373/linux-training/blob/main/images/shell-course/pipes-1.jpg width=50% height=50% >

> Օրինակ

`ls /usr/bin | sort -r`

Նույնը չէ, ինչ հաջորդաբար կատարումը ;-ով

`ls /usr/bin ; sort -r`

> Հրամանների համակցում

Հրամանները կարելի է համակցել հետևյալ կերպ՝

* **&&**	Logical AND 
    եթե առաջին հրամանի ելքի կոդը (exit code) 0 է, կատարել երկրորդը

* **||**	Logical OR
    եթե առաջին հրամանի ելքի կոդը (exit code) 0 չէ, կատարել երկրորդը

* **;**	Պարզապես կատարել հրամանները՝ մեկը մյուսի հետևից

* **|**	Փոխանցել առաջին հրամանի ելքի տվյալները (stdout) 
    երկրորդ հրամանի մուտքին (stdin)

_( **echo $?**  - ցույց է տալիս վերջին հրամանի ելքի կոդը (exit code) 0=OK)_

<br><br>

### Access files

There are several tool to view text files contents.

> **less** - view/browse text file page-by-page

* **Enter/DOWNARROW**	– մեկ տող ներքև
* **SPACE/PgDn**		– մեկ էկրան ներքև
* **PgUp/b**			– մեկ էկրան վերև
* **UPARROW**			– մեկ տող վերև
* **/**					– որոնում
* **Home**				– անցնել տեքստի սկիզբը
* **End**				– անցնել տեքստի վերջը
* **q**					– ելք

> Օրինակներ
 
`less /etc/services`
`ls /usr/bin | sort -r | less`

<br><br>

> **cat** - output whole file to STDOUT (default - terminal)

> Օրինակներ
 
`cat /etc/services`

`cat /etc/services | sort -r `

`cat /etc/services | sort -r | less`

<br><br>

> **head** - output some first lines (default 10) of file STDOUT (default - terminal)

> Օրինակներ
 
`head /etc/services`

`head -1 /etc/services`

`head -1 /etc/services > /tmp/h1`

`head -1 /etc/services >> /tmp/h1`
 
<br><br>

> **tail** - output some last lines (default 10) of file STDOUT (default - terminal)

> Օրինակներ
 
`tail /etc/services`

`tail -1 /etc/services`

`tail -1 /etc/services > /tmp/s1`

`tail -1 /etc/services >> /tmp/s1`

<br><br>

> **grep** - filter lines based on pattern

> Օրինակներ
 
`cat /etc/services | grep http `

`ls /usr/bin | grep log`

`ls /usr/bin | grep ^log`

`ls /usr/bin | grep log$`

<br><br>

> **awk**  - extract sections/fields from each line of files

> Օրինակներ

`awk -F":" '{print $1}' /etc/passwd | grep ^s`

`tail -10 /etc/passwd | awk -F":" '{print $3"--"$1}' | sort -n`

`cat /etc/passwd | grep -E ^'(b|sy)' | awk -F":" '{print "User: "$3"  "$1}'`


<br><br>
### Text Editors (Խմբագիրներ)

* **vi /vim**	Standard UNIX editor
* **nano**		Simple display-oriented text editor 
* **mcedit** 	Midnight Commander internal editor
* **joe** 		Joe editor
* **gedit/kate** 	Graphical editors 


#### Vim/Vi basics

Vim/Vi is a very powerful editor Linux/Unix text editor. The reason to know it's basics is that it is initially available almost on any Linux/UNIX system.
Even if any other editor will not be present or available to install Vi/Vim will be there to enable you editing text files.
(to learn more than below basics you can type `vimtutor` and follow instructions)

> Vim Modes
* **Insert**	- Insert text by typing
* **Execute**	- Execute commands within the editor
* **Command**	- Perform different editing actions using single keystrokes
* **Visual**	- Highlight or select text for copying, deleting, etc

<img src=https://github.com/arthur7373/linux-training/blob/main/images/shell-course/vim-modes.jpg width=50% height=50% >

> Execute Mode Commands

* **:q**	- Quit when no changes have been made after last save
* **:q!**	- Quit ignoring changes made
* **:wq**	- Save current file and quit
* **:w {file name}** - Save file with specified name

<br><br>

### Shell scripting basics

> First line of Shell script should look like: 
* `#!/bin/bash`
* `#!/bin/sh`

After 2 special characters **#!**, 
it should contain the path to the interpreter - program that will try to interpret the text line by line and do what is required.

In case script don't have such first line, it will still work, but it will be interpreted by current shell and chances are, there will be some errors. 

<br><br>

*Simple script example*

```bash
cat  > ~/s1  << "EOF1"
#!/bin/bash
ls -l /usr/bin/
EOF1
chmod +x ~/s1

```

Try running this simple script:

`./s1`

Let's now understand what was done above.

We used method called _Here document_ to create the script and made it executable with `chmod`.
The script itself is a single `ls` command, that outputs detailed (-l) contents of directory _/usr/bin/_


<br><br>

### Positional Parameters

During running, shell scripts have access to special data from the environment:

* **$0** or **{$0}** - The name of the script
* **$1** or **{$1}** - The first argument sent to the script 
* **$2** or **{$2}** - The second argument sent to the script
...
* **$*** - all arguments as one
* **$#** - count/number of arguments

This enables to pass some data to the script by means of positional parameters.

> Example of positional parameters

```bash
cat  > ~/s2  << "EOF1"
#!/bin/bash
# Here we get the first positional parameter and provide it to "ls" command
ls -l ${1}
EOF1
chmod +x ~/s2

```

Now try running this simple script:

```bash
./s2
```

> QUESTION: What directory did `ls` command list ?  Why ?

Now try providing first positional parameter

```bash
./s2 /tmp
```

```bash
./s2 /usr/sbin
```

As you see we pass the data to the script, which changes how `ls` command works.


Let's now pass more data. 
We will provide options to `ls` via first positional paramater, 
the directory to show via second and pattern to filter lines via third.

```bash
cat  > ~/s3  << "EOF1"
#!/bin/bash
ls ${1} ${2} | grep ${3}
EOF1
chmod +x ~/s3

```

First try running this script without parameters:

```bash
./s3
```

> EXPLAIN THE OUTPUT


Now try providing need 3 positional parameters

```bash
./s3 -lh /bin log
```

```bash
./s3 -r / l
```

### Variables

Shell variables - temporary storage for information.

Shell does not care about the type of variables. 
Variables could store strings, characters or integers. 

Variable names are uppercase by convention, but lowercase and other symbols can be used as well.

Syntax: **VARNAME=VALUE**

> Note: There should be no space around “=” sign 

Prefix the variable name with **$**, gives the value stored in that variable.

The following script creates a variable called **NAME** and assigns the value "HELLO STUDENT". 


Example of simple variable assignment usage

```bash
cat  > ~/v1  << "EOF1"
#!/bin/bash
NAME="HELLO STUDENT"
echo $NAME
EOF1
chmod +x ~/v1

```

Execute the above script, which will output the text to the terminal.

**Task: Modify the script to output 1-st positional parameter after HELLO STUDENT.**

<br><br>

When you work in shell, there are already many defined shell variables.

**Global variables** (also called **environment variables**) - available to all shells. 
The `env` or `printenv` commands can be used to display environment variables. 

**Local variables** are visible only within the block of code.  
Using the `set` built-in command without any options will display a list of all variables 
(including environment variables) and functions.  

In a function, a local variable has meaning only within that function block. 

```bash
set | grep HIST
```

```bash
set | grep NAME
```

```bash
env | grep NAME
```


### Conditionals

Very frequently there is need to make decisions based on certain conditions. Conditions are expressions that after being evaluated return "yes" or "no" (i.e. true or false).

Most used is **if** conditional

Simple example is below:

```bash
cat  > ~/c1  << "EOF1"
#!/bin/bash
a=5
b=30

if [ $a -lt $b ]
then
        echo "$a < $b"
fi
EOF1
chmod +x ~/c1

```

Execute the above script, which will output the text to the terminal.

**Task: Modify the script to get 2 variables from 2 positional parameters**

<br><br>
Notice that in case **a** is NOT less that **b**, nothing is printed.
Let's add that variant too.

Edit the file and add 

```bash
else
        echo "$a > $b"
```
before `fi` line


if you run the above script without parameters you see output is not so pretty.
Now let's add additional check if parameters are present.

Add below code just after first line `#!/bin/bash`

```bash
if [[ $# < 2 ]] 
then 
  echo "Please provide 2 numbers as parameters"
  echo "Usage: $0 num1, num2" 
exit 
fi 
```

Now you may notice that even thoigh we check for the number of parameters to be at least 2, 
if we give non-numeric parameter it will give error.


```bash
./c1 aaa 1 
```


To implement checking if 1st parameter is numeric, add below code just after above check `#!/bin/bash`

```bash
if [ $1 -eq $1 2>/dev/null ]
then
echo -n
else
echo "$1 not number"
exit
fi
```

Now check if it works

```bash
./c1 aaa 1 
```

But second parameter still is not checked.

```bash
./c1 1 aaa 
```

**Task: Modify the script to check 2nd positional parameter as well**


### Functions

We see that in above code we add the same part for checking 1st parameter, then 2nd.
In case we don't want to repeat the same code twice, we can create a **function**.

```bash
cat  > ~/f1  << "EOF1"
#!/bin/bash
if [[ $# < 2 ]]
then
  echo "Please provide 2 numbers as parameters"
  echo "Usage: $0 num1, num2 ..."
exit
fi

isnumber () 
{ 
if [ $1 -eq $1 2>/dev/null ]
then
echo -n
else
echo "$1 not number"
exit
fi
}


a=${1}
b=${2}

isnumber $a
isnumber $b

if [ $a -lt $b ]
then
        echo "$a < $b"
else
        echo "$a > $b"

fi

EOF1
chmod +x ~/f1

```

You can see that the above script `f1' works the same way as 'c1',
but here we define and use function **isnumber**.


Other example of function

```bash
cat > ~/f2 << "EOF1"
#!/bin/bash 

exf () {  
echo "We learn $1" 
} 

exf Linux 
exf Shell
exf Programming in Linux
exf Shell Programming in Linux

EOF1
chmod +x ~/f2

```

> Here you may understand that INSIDE function **$1** means NOT 
> first parameter of the script, but first parameter of that function

Now notice that in last 2 lines only first word is printed.
Why?

**Task: Modify the script to print complete lines.**
**HINT: you need to use something else than $1** 


### Sourcing Scripts

### Error handling, Exit Status

### Loops



```bash
#!/bin/bash
if [ -z $1 ]; then
echo "Usage: $0 number of loops"
exit
fi
clear
COUNTER=0
while [ $COUNTER -lt $1 ]
do
echo "State (for $1 seconds)"
echo "second:$COUNTER" 
echo "-- Users --"
w
echo "----------------"
/bin/sleep 1
clear
COUNTER=`expr  $COUNTER + 1`
done
```




```bash
#!/bin/bash
echo "How do you like it:"
for (( i=1; i<=5; i++ ))
do
    for (( j=1; j<=i;  j++ ))
    do
     echo -n "$i"
    done
    echo ""
done
```


### Arrays
### Text Processing Tools

