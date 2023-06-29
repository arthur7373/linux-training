# Linux Administration and Networking Basics (level 2) <br /> Linux-ի կառավարում և ցանցային հիմունքներ (փուլ 2)

## Activity Monitoring

### psacct/acct

**psacct**/**acct** is a service for monitoring users and applications.

With it administrator can see how long users were connected, how much of the resources they used and what commands they run. It also allows to evaluate the resources that applications are consumed on your server.  

Install Process Accounting
`yum install psacct`
or on Ubuntu/Debian
`apt install acct`

Enable and start the service
`systemctl --now enable psacct`
or on Ubuntu/Debian
`systemctl --now enable acct`


#### Connect Time

**ac** command provides a total connect time in hours is based on logins and logouts.

Without any argument it will display total statistics of connect time in hours based on the user logins/logouts from the current wtmp file.

`ac`

On daily basis

`ac -d`

Per User

`ac student`

`ac -d`

`ac -p`

#### Commands of Users

**lastcomm** command provides previously executed commands.

By user

`lastcomm --user student`

By command

`lastcomm --command grep`

By tty

`lastcomm --tty pts/2`

