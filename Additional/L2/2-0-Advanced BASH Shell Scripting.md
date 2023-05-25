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

```bash
$ cat  >sample.sh
#!/bin/bash
LIST="/usr/bin/"
ls -l $LIST
```

Execute the above script, which will list the /usr/bin in long format.

**Task: Modify the script to work with 1-st positional parameter.**

