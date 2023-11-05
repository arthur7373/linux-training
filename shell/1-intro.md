# Shell programming in Linux

## Linux Terminal (Լինուքսի հրամանների տողը)

> Ներածական հարցեր թեմայի վերաբերյալ 
* Ծանո՞թ եք տերմինալի միջոցով Linux-ում աշխատելուն:
* Եթե այո ի՞նչ ծրագրի միջոցով:
* Ծանո՞թ եք SSH-ով միանալուն:
  * Putty
  * Xshell
  * MobaXterm
  * Windows Subsystem for Linux (WSL) in Windows 10/11
  

Լինուքս համակարգ մուտքագրվելու համար պետք է տրամադրել անունը (username) և գաղտնաբառը (password):

SSH-ով միանալու պարագայում գաղտնաբառի փոխարեն հնարավոր է մուտքը զույգ-բանալիներով (Public/Private Keys)

Մուտքագրվելիս Լինուքս համակարգ հնարավոր է աշխատել հետևյալ տարբերակներից մեկով. 
* Graphical User Interface (GUI)
* Command Line Interface (CLI) / Terminal

Այս դասընթացի ընթացքում մենք կաշխատենք երկրորդ տարբերակով:

<br><br>

## Linux VM remote access

**Oracle VM VirtualBox** միջավայրում միացրեք **Ubuntu-22.04** վիրտուալ մեքենան


## Linux Terminal, CLI Basics

> Ինչ է Terminal-ը

Ծրագիր, որի ներսում գոծում է հրամանի տող (Command Line): 
Հնարավոր է դա լինի հեռակա միացումով (remote connection/access):
Այսինքն աշխատանքը իրականացվի ցանցով կապված մեկ այլ հեռակա համակարգի վրա:

> Ինչ է Command Line Interface/Interpreter (CLI) / Shell 

Ծրագիր, որը իրականացնում է հրամանի տողի աշխատանքը, 
վերլուծում է (interpret) տողը ու կատարում հրամանը կամ հայտնում սխալի մասին:

Հրամանները վերլուծվում են տող առ տող, այդ պատճառով ամեն մի հրամանը պետք է ավարտվի **[Enter]**-ով:
** [Enter]**-ից հետո հրամանը այլևս հնարավոր չէ փոխել:

> **#**  նշանը և դրանից հետո ամեն ինչը համարվում է comment և չի վերլուծվում:  

CLI այլ կերպ նաև անվանվում է Shell: Լինում են տարբեր տեսակներ, ամենատարածվածը` **Bash**

CLI/Shell/Bash աշխատում է ինտերակտիվ եղանակով, որը նաև անվանում են REPL 

**Read**->**Evaluate**->**Print**->**Loop**  



> Լինուքսի յուրահատկությունը

* Մեծատառի/փոքրատառի տարբերություն (LiNuX iS CaSe SeNsItIvE)
  * Հրամանների և ծրագրերի անվանման մեջ 
    * `command`
    * `Command` 
    * `COMMAND` 
  * Ֆայլերի և դիրեկտորիաների/ֆոլդերների անվանման մեջ
    * `file`
    * `File`
    * `FILE`
  * Օգտագործողների և խմբերի անվանման մեջ
    * `user`
    * `User`
    * `USER`


> Command Prompt

Տողային հարաման մուտքագրելու հրավեր:

Օրինակներ.

**$** - User Prompt

**#** - ROOT Prompt

Այս հրավերի տեսքը հնարավոր է փոխել, 
սակայն հաճախ նշված նշանները պահպանվում են 
և ավելանում է լրացուցիչ տեղեկություն, օրինակ՝ սերվերի, օգտագործողի, տվյալ դիրեկտորիայի անունները:

`student@server:/usr/bin$`

<br><br>

> Movement in command line

Աջ/ձախ սլաքներից բացի հրամանային տողում տեղաշարժ 	
* `Ctrl-A` – տեղափոխել տողի սկիզբ
* `Ctrl-E` – տեղափոխել տողի վերջ


> Command structure

Հրամանների կառուցվածք

<img src=https://github.com/arthur7373/linux-training/blob/main/images/shell-course/command-structure.png width=50% height=50% >

> Հրամանների օրինակներ


* echo - display a line of text 
```bash
echo We learn Linux # this is a comment 
```
```bash
# echo We learn Linux # this is a comment
```

```bash
echo "$USER" learns Shell Programming
```
```bash
echo My Shell is: "$SHELL"
```

* sleep - delay for a specified time 

```bash
echo ; echo -n "Be patient " ; sleep 2 ; echo -n "to learn " ; sleep 2 ; echo "Shell Programming in Linux" ; sleep 2 ; echo 

```

* id - display user information

```bash
id
```
```bash
id --help
```
```bash
id -n -u
```

* date - display date

```bash
date
```
```bash
date --help
```
```bash
date +"%d-%m-%Y"
```

> Command History 

Նախորդ հրամանները հիշվում են, որ նորից նույնը չհավաքենք:

* Վերևի սլաքը (Up Arrow) նախորդ հրամանները
* Որոնում նախորդ հրամաններում `Ctrl-R`
  * մուտքագրեք հրամանի սկիզբը
  * Կրկնելով `Ctrl-R` հնարավոր է փնտրել նախորդ տարբերակները
    * Օրինակ՝ սեղմեք `Ctrl-R` և հավաքեք ՝da`
    * Կրկնեք `Ctrl-R`
  

> Filename/Command completion 	

Հնարավոր է ամբողջությամբ չլրացնել հրամանի/ֆայլի անունը՝ համակարգը կարող է ամբողջացնել անունը

* `[Tab]`	հրամանի/ֆայլի լրացում, եթե դա միակ տարբերակն է
  * Օրինակ՝ հավաքեք ՝sle` և `[Tab]`

<br><br>
* `[Tab] [Tab]` եթե մեկից ավել տարբերակ կա, ապա ցուցադրվում են բոլոր տարբերակները
  * Օրինակ՝ հավաքեք ՝sl` և `[Tab] [Tab]`

<br><br>

## File Management Commands

> Ֆայլերի անվանումը
* Windows
  * `C:\Program Files\Oracle\VirtualBox\VirtualBox.exe`

* Linux/UNIX

  * `/home/user1/docs/letter.txt`
  * `/bin/ls`


### Ծանոթացում ֆայլային համակարգի կառուցվածքին

```bash 
ls /
```

```bash
man file-hierarchy
```

> Ավելին իմանալու համար. https://en.wikipedia.org/wiki/Filesystem_Hierarchy_Standard 


Երբ մուտքագրվում եք / բացվում է Terminal-ը, հայտնվում եք ֆայլային համակարգի ընթացիկ դիրեկտորիայում:

> Հիմնական հրամաններ
* `pwd` - ընթացիկ դիրեկտորիան
* `cd` - փոխել ընթացիկ դիրեկտորիան
  * Օրինակ՝ հավաքեք ՝cd /h` և `[Tab]` հետո ևս մեկ `[Tab]`
  * Օրինակ՝ հավաքեք ՝cd /u` և `[Tab]` հետո `lo` և `[Tab]` հետո `b` և `[Tab]`


* `ls` - ֆայլերի ցուցակ
<hr>

`ls [options] <directory/file>`

* `-l`     ընլայնված ցուցակ
* `-a`  ցույց տալ բոլոր ֆալերը  (նեռարյալ .-ով սկսվող ֆալերը )
* `-S`   դասավորել ֆայլերը ըստ չափի (–lS)
* `-r`  Հակադարձ դասավորման կարգով (–lSr)
* `-h`    Մարդու համար ավելի ընթեռնելի (ֆայլերի չափը k, M, G-ով)


<hr> 

> ՕԳՏԱԿԱՐ ԿԱՅՔ՝ 
> https://explainshell.com/
> հրամանների մանրամասն բացատրություն: 
> Բացեք կայքը և մուտքագրեք հրաման, օրինակ
```bash
id -n -u
```

կամ

```bash
echo -n "Be patient " ; sleep 2 ; echo -n "to learn " ; sleep 2 ; echo "Shell Programming in Linux" ; sleep 2
```
<hr> 

> Հատուկ անվանումներ
* `/`   Գլխավոր դիրեկտորիան
* `.`    Տվյալ դիրեկտորիան
* `..`   Նախորդ (վերևի) դիրեկտորիան
* `~`    Օգտագործողի անձնական դիրեկտորիան
* `.`-ով սկսվող ֆայլերը սովորաբար օգտագործվում են անհատական կարգավորումները պահելու համար:
  (կետը տվյալ դեպքում ֆայլի անվանման մասն է)

Օրինակներ՝

`./a`        նույնն է ինչ   `a`

`../home/student`  մեկ մակարդակ վերև և home/student


<hr>

> Հիմնական հրամաններ
* `touch`                    Ստեղծել դատարկ ֆայլ
* `cp <fromfile> <tofile>`   	Պատճենել ֆայլը
* `mv <fromfile> <tofile>`	Տեղափոխել / վերանվանել ֆայլը
* `rm <file>`  			Հեռացնել ֆայլ/դիրեկտորիա
* `mkdir <newdir>`		 Ստեղծել դիրեկտորիա 
* `alias <alias> <command>` Ստեղծել հրամանի կրճատում 
* `type <command>` 		 Հրամանի գտնվելու վայրը և այլ տեղեկություններ
* `stat <path-to-file>`  Տեղեկություններ ֆայլի մասին


> Հրամանների օրինակներ

```bash 
cd /home ; pwd ; ls -la
```
```bash 
cd ; ls -la /home
```
```bash 
mkdir d1 ; cd d1 ; pwd ; touch f1 ; ls f*
```
```bash 
cp f1 f2 ; ls f*
```
```bash 
mv f2 f3 ; ls f*
```
```bash 
alias del='rm -i'
```
```bash 
del f*
```
```bash 
cd ~ ; rm -r d1
```

<hr>

```bash 
type cd
```
```bash 
type id
```

```bash
stat /usr/bin/id
```

<hr>

```bash 
cp -r /etc  ~`
```
```bash 
mkdir ~/TEST`
```
```bash 
mv  ~/etc  ~/TEST`
```
```bash 
rm -r ~/TEST`
```



<br><br>


## Access files

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
## Text Editors (Խմբագիրներ)

* **vi /vim**	Standard UNIX editor
* **nano**		Simple display-oriented text editor 
* **mcedit** 	Midnight Commander internal editor
* **joe** 		Joe editor
* **gedit/kate** 	Graphical editors 


### Vim/Vi basics

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

## Shell scripting basics

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

Check the contents of the script you created:

```bash
cat ~/s1
```

<br><br>

## Positional Parameters

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

Now try running this simple script without any parameter:

```bash
./s2
```

> QUESTION: What directory did `ls` command list ?  Why ?

Now try providing one positional parameter

```bash
./s2 /tmp
```

```bash
./s2 /usr/sbin
```

As you see we pass the data to the script, which changes how `ls` command works.


Let's now pass more data. 
We will provide options to `ls` via first positional parameter, 
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


Now try providing all 3 positional parameters

```bash
./s3 -lh /bin log
```

```bash
./s3 -r / l
```

## Variables

Shell variables are temporary storage for information.

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

**Task 1: Modify the script to output 1-st positional parameter after HELLO STUDENT.**

**Task 2: Have fun with _cowsay_**

1. Install `cowsay` program
```bash
yum -y install cowsay
```

2. Run it
```bash
cowsay Hi student
```

It can draw different pictures and say the text you provide.


3. Create an alias `krya` to draw **turtle** saying what you will give as parameter.
   1. List of pictures are available with
   ```bash 
   cowsay -l
   ```
   2. Read `man cowsay` and find the option to provide **turtle** picture file as parameter.
   3. Your alias should work like `krya BAREV`


![img.png](../images/shell-course/turtle.png)

4. Create the **script** which will do the same as **alias**. 

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


## Conditionals

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


## Functions

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


#### PRATICE
Based on previous use of funny `cowsay` program, create more flexible script `nkar` to get 2 parameters and provide to `cowsay`
1. what picture to draw
2. what text to say

When ready it should work like `nkar elephant HELLO`

![img.png](../images/shell-course/elephant.png)

## Sourcing Scripts

## Error handling, Exit Status

## Loops



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


## Arrays
## Text Processing Tools

