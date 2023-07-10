# Linux Administration and Networking Basics (level 2) Linux-ի կառավարում և ցանցային հիմունքներ (փուլ 2)



##  Advanced BASH Shell Scripting


*Quick initial script example*

```bash
#!/bin/bash
if [ -z ${1} ]; then
echo "Usage: $0 number of loops"
exit
fi
echo "How do you like it:"
for (( i=1; i<=${1}; i++ ))
do
    for (( j=1; j<=i;  j++ ))
    do
     echo -n "$i"
    done
    echo ""
done
```

### Variables
Bash variables - temporary storage for information
Bash does not care about the type of variables. 
Variables could store strings, characters or integers. 

Variable names are uppercase by convention, but any symbols can be used.

Syntax:
VARNAME=VALUE
Note: There should be no space around “=” sign 

The following script creates a variable called LIST and assigns the value “/usr/bin”. 
Prefix the variable name with $, which will give the value stored in that variable.

#### PRACTICE

*Example.1 Simple Bash Variable Assignment Usage*

$ cat  >sample.sh

```bash
#!/bin/bash
LIST="/usr/bin/"
ls -l $LIST
```

Execute the above script, which will list the /usr/bin in long format.

**Task: Modify the script to work with 1-st positional parameter.**

### Bash Variable Scope – Local and Global 

In Bash, variables do not have to be declared. When you access the variable which is not used so far, 
you will not get any warning or error message. Instead, it will display a blank value. 

*Example 2. Blank values in bash variables* 

`$ cat > var1.sh`

```bash
#!/bin/bash 
echo "Variable value is: $VAR1" 
VAR1="LINUX" 
echo "Variable value is: $VAR1" 
```

Initially the variable will have a blank value, after assigning, you can get your values.  
export command is used to export a variables from an interactive shell.  
export shows the effect on the scope of variables. 

`$ VAR1=UNIX ; ./var1.sh`

 
Still you will get blank value for variable VAR1. The shell stores variable VAR1 with the LINUX only in 
the current shell. During the execution of var1.sh, it spawns the subshell and it executes the script. So 
the variable VAR1 will not have the value in the spawned shell.  
You need to export the variable for it to be inherited by another program – including a shell script, as 
shown below. 

`$ export VAR1=UNIX ; ./var1.sh `

Now, you can notice that after execution of the shell script var1.sh, the value of VAR1 is UNIX. Because 
the variables will not be passed back to your interactive shell, unless you execute the script in the 
current shell 


**Global variables** (also called **environment variables**) - available to all shells. 
The `env` or `printenv` commands can be used to display environment variables. 

**Local variables** are visible only within the block of code.  
Using the `set` built-in command without any options will display a list of all variables 
(including environment variables) and functions.  

In a function, a local variable has meaning only within that function block. 

`$ set | grep HIST` 

The following table lists some important Bash internal variables that can be redefined:  

| $TMOUT Logout after being idle for defined seconds | 
export TMOUT=3 
| --- | --- |

$EDITOR Default editor 
export EDITOR=/usr/bin/nano ; crontab -e 
$HISTFILE Location of command history file 
echo $HISTFILE 
$HISTSIZE Maximum number of lines in the history of each session 
(added to history file after successful session end) 
echo $HISTSIZE 
$HISTFILESIZE Maximum number of lines in the history file 
echo $HISTFILESIZE 
$HISTCONTROL Control how to store command history. For example: ignore duplicate commands 
and/or ignore commands that have leading whitespace.(can be set to 
ignorespace, ignoredups or ignoreboth ) 
$ echo "HISTCONTROL=ignoreboth" >>~/.bashrc 
$HISTIGNORE Do not store in the command history lines containing this string 
HISTIGNORE="*echo*" 
echo $UID   
up arrow will not give you echo 
$PS1 Main prompt string for command line 
export PS1="[\@] \u@\h> " 
$PS2 Second prompt string 