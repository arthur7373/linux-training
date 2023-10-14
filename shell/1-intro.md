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
