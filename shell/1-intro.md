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
 
![](../images/shell-course/command-structure.png)

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


### File Permissions

![](../images/shell-course/permissions.png)


![](../images/shell-course/permissions2.png)

![](../images/shell-course/permissions3.png)

![](../images/shell-course/permissions4.png)

![](../images/shell-course/chmod.png)

![](../images/shell-course/mc.png)

![](../images/shell-course/umask.png)




### I/O Redirection, Pipes
### Text Editor
### Sourcing Scripts
### Variables
### Positional Parameters
### Error handling, Exit Status
### Conditionals
### Loops
### Functions
### Arrays
### Text Processing Tools

