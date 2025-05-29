# Linux Administration and Networking Basics (level 2) Linux-ի կառավարում և ցանցային հիմունքներ (փուլ 2)


## Advanced Shell Scripting

Based on initial knowledge from Level 1 course let's now go deeper in shell scripting.

Let's remember variable usage.

### Some useful info

During running, shell scripts have access to special data from the environment:

* **$0** or **${0}** - The name of the script
* **$1** or **${1}** - The first argument/positional parameter sent to the script 
* **$2** or **${2}** - The second argument/positional parameter sent to the script
...
* **$*** - all arguments/positional parameters as one
* **$#** - count/number of arguments/positional parameters

Command substitution:
* **$()** or **\`   \`**



> NOTICE: Below we use do 2 things to create some script:
> 1. we use method called _Here document_ to create the script 
> 2. then we make it executable with `chmod`
> 
### Example with Variables

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
sudo yum -y install cowsay
```

2. Run it
```bash
cowsay Hi Linux student
```

It can draw different pictures and say the text you provide.


3. Create `anim` **script** which draws other animal mentioned as 1-st parameter, saying what you will give 2-nd parameter.
   1. List of pictures are available with
   ```bash 
   cowsay -l
   ```
   2. Find the option to select picture
      1. ```bash
         cowsay -h
         ```
      2. ```bash
         man cowsay
         ```
   3. Script should work like `anim elephant HELLO`

Example:
![img.png](images/elephant.png)


## Variables - Local & Global

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
**HINT: you need to use something else than '$1'** 


## Sourcing Scripts

Sourcing script means including one script into another

It may be useful if you have a code block you may want to: 
* separate or 
* use in multiple scripts

Simple example of it is in `~/.bashrc`
```bash
cat ~/.bashrc
```

Here we see sourcing is used multiple times like:
```bash
if [ -f ~/.bash_aliases ]; then
    . ~/.bash_aliases
fi
```

Let's separate two blocks in above `f1` script and source them.

```bash
cat  > ~/f11-s1  << "EOFs1"
if [[ $# < 2 ]]
then
  echo "Please provide 2 numbers as parameters"
  echo "Usage: $0 num1, num2 ..."
exit
fi
EOFs1

cat  > ~/f11-s2  << "EOFs2"
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
EOFs2


cat  > ~/f11  << "EOF1"
#!/bin/bash

. ~/f11-s1

. ~/f11-s2

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
chmod +x ~/f11

```

Note we didn't made `f11-s1` and `f11-s2` executable, because they will not be called directly.


## Conditionals - Case

`case` statement is the simplest form `if-then-else` statement.
It is generally used to when you have multiple different choices.

Examples:

```bash
cat  > ~/case1  << "END777"
#!/bin/bash

case "$1" in

'start')
echo "Starting ..."
sleep 2
echo "Started ..."
;;

'stop')
echo "Stopping ..."
sleep 2
echo "Stopped ..."
;;

'restart')
echo "Restarting ..."
sleep 2
echo "Restarted ..."
;;

*)
echo "Usage: $0 [start|stop|restart]"
;;

esac
END777
chmod +x ~/case1

```


Another case example 

```bash
cat  > ~/case2  << "END777"
#!/bin/bash
shopt -s nocasematch # Here we activate "nocasematch" shell option to make pattern case insensitive
echo "Enter the name of a month" 
echo "and I will tell the number of days in it"
echo "(q - to finish)."

while true
do
read month
case $month in
February|feb)
echo "28/29 days in $month.";;

April|apr|June|june|September|sep|November|nov)
echo "30 days in $month.";;

jan|mar|may|jul|aug|oct|dec|January|March|May|July|August|October|December)
echo "31 days in $month.";;

q) echo "Bye!" 
   exit;;

*) echo "Unknown month $month. Please try again";;
esac
done
END777
chmod +x ~/case2

```


## Loops
Count factorial of a number (with `for` loop)

```bash
cat  > ~/loop3  << "END5"
#!/bin/bash
num=$1
fact=1
for((i=2;i<=num;i++))
{
  fact=$((fact * i))  #fact = fact * i
}
echo $fact
END5
chmod +x ~/loop3

```

**Task: Add here the check if positional parameter is number and exit if it is not given (you may `source` parts of previous scripts).**

<br><br>

Count factorial of a number (with `while` loop)

```bash
cat  > ~/loop4  << "END5"
#!/bin/bash
num=$1
fact=1
while [ $num -gt 1 ]
do
  fact=$((fact * num))  #fact = fact * num
  num=$((num - 1))      #num = num - 1
done

echo $fact
END5
chmod +x ~/loop4

```

**Task: Add here the check if positional parameter is number and exit if it is not given (you may `source` parts of previous scripts)**

<br><br>


## Arithmetic Expansion. Double-Parentheses Construct. 

Arithmetic operations in Bash can be done in several ways. 

1. ‘Old’ ways were using let  
`let a="1+6" ;echo $a `

2. or expr 
`b=`expr 2 + 5` ; echo $b` 
3. Newer versions of Bash have other way: double parentheses. 
**(( ... ))** construct permits arithmetic expansion and evaluation. 
In its simplest form, _a=$(( 5 + 3 ))_ would set a to 5 + 3, or 8. 
However, this double-parentheses construct is also a mechanism for allowing C-style manipulation of 
variables in Bash, for example increments like (( var++ )) or (( var+=5 )). 
`$ z=$((4+3)); echo $z `


Example of a shell script  that calculates the average of all command line parameters 

```bash
cat > ~/aver.sh << "EOF1"
#!/bin/bash 
if [[ $# = 0 ]] 
then echo "Usage: $0 num1, num2 ..." 
exit 
fi 
(( m= 0 )) 
isnumber () 
{ 
if [ $1 -eq $1 2>/dev/null ] 
then  
((m+=1)) 
else
echo "*******"
echo "ERROR: $1 is not a number" 
echo "*******"
 exit $? 
fi 
} 
(( sum=0 )) 
for i in $* 
do 
if  isnumber $i 
then  
((sum+=$i)) 
fi 
done 

((AV=sum/m)) 
echo "For $* " 
echo  “Average is $AV” 
EOF1
chmod +x ~/aver.sh

```

Run with non digit argument
```bash
./aver  5 8 13 77 AAA
```
**Task: Modify the above script not to exit in case of non digit argument. _HINT: you need to avoid script exiting on that error. This can be done in several ways either commenting appropriate line or changing it to `return` command:_**

<br><br>


Count sum of all digits in a number with `while` loop

```bash
cat  > ~/sumd  << "END5"
#!/bin/bash
num=$1
sum=0

while [ $num -gt 0 ]
do
    mod=$((num % 10))    #Split last digit by modulo 10 - remainder of a division by 10
    sum=$((sum + mod))   #Add that digit to sum
    num=$((num / 10))    #Divide num by 10 
done

echo $sum

END5
chmod +x ~/sumd

```

**Task: Add here the check if positional parameter is number and exit if it is not given (you may `source` parts of previous scripts).**

<br><br>

## Text Processing Tools

<br><br>

#### Advanced Text Processing - AWK 

> **AWK**  - extract sections/fields from each line of files


Examples

```bash
awk -F":" '{print $1}' /etc/passwd | grep ^s
```

```bash
tail -10 /etc/passwd | awk -F":" '{print $3"--"$1}' | sort -n
```

```bash
cat /etc/passwd | grep -E ^'(b|sy)' | awk -F":" '{print "User: "$3"  "$1}'
```

```bash
cat /etc/passwd | awk -F":" '/nologin$/ {print $1"-"$5}'
```

**Task:Modify the above command, to narrow selection by only lines starting with 's'** 


<br><br>

#### Advanced Text Processing – SED 

Sed is a very useful **S**tream **ED**itor.  
It's ideal for batch-editing files or for creating shell scripts to modify existing files in powerful ways. 
It's rather complex for quick full understanding, so below are only few use cases.

One of sed's most useful commands is the _**substitution**_ command. 

Following command takes a stream from pipe and replaces first occurrence of `:` on each line to `<*>`: 

```bash
cat /etc/passwd | sed -e 's/:/<*>/'
```

To replace all occurrences we should add `g` to make replacement global: 

```bash
cat /etc/passwd | sed -e 's/:/<*>/g' 
```

Another useful examples with SED: 

Output lines `5-7` 

```bash
sed -n '5,7p' /etc/group
```

**-n** causes not to output each processed lines<br>
**p** command specifies print (output) specified line range: 5-7 


Output all lines except `1-20` 

```bash
sed '1,20d' /etc/group
```

**d** command causes specified line range: 
`1-20` to be deleted/removed from output, 
other lines will be present in output 

Remove comments (lines starting with '#' - `^#`) and empty lines `^$` from output:  

```bash
sed '/^#\|^$/d' /etc/rsyslog.conf
```

**d** command causes specified lines: <br>
**^#** - starting with **#** <br>
or **\\|** <br>
**^$** - empty line (**^**- line start, **$** - line end) 
to be deleted/removed from output, 
other lines will be present in output. 

**Task: Modify the above command, to remove also lines starting with '$'** 


Generate a log file with 200 numbered lines.
(here `s/^/.../` is a `sed` substitution that matches the **start of each line** (**^**) and adds text `Log line number` there)

```bash
seq 1 200 | sed 's/^/Log line number /' > archive.log
```

