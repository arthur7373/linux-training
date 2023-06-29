# Linux Administration and Networking Basics (level 2) <br /> Linux-ի կառավարում և ցանցային հիմունքներ (փուլ 2)

## Screen manager

Screen  is  a  remote terminal session manager that multiplexes a single connection between several windows (typically interactive shells). It provides the detaching/reattaching functionality. See man screen for more info.

`screen -RD`

Each process gets its own virtual window, and you can bounce between virtual windows interacting with each process. The processes managed by screen continue to run when their window is not active. Screen offers the ability to detach from a session and then attach to it at a later time. When detached from a session, the processes screen is managing continue to run. You can then re-attach to the session at a later time, and your terminals are still there, the way you left them.

Before actually running screen, it's important to understand how to interact with it. Screen sends all entered text to the current window, with the exception of the command character. The default command character is Ctrl-a (press the Ctrl and the a key at the same time).
The command character is used to notify screen that you'd like to control screen itself, rather than the application in the current window. The key pressed after the command character designates which screen command you would like to perform. Some of the more useful commands and their key bindings are shown in table below:
Keys bindings in screen:
```bash
Ctrl+a c		new window
Ctrl+a n		next window
Ctrl+a p	previous window
Ctrl+a d		detach screen from terminal.	Run 'screen -RD' to reattach
Ctrl+a A	set window title
Ctrl+a k	kill current window
```

Screen has system-wide configuration file: `/etc/screenrc`.
Each user can create `~/.screenrc` personal configuration file.


#### PRACTICE

1. Create “~/.screenrc” config file:  
```bash
# Use bash
shell /bin/bash
autodetach on
# Big scrollback
defscrollback 5000
# No annoying startup message
startup_message off
# Display the status line at the bottom
hardstatus on
hardstatus alwayslastline
hardstatus string "%{.kW}%-w%{.bW}%t [%n]%{-}%+w %=%{..G} %H %{..Y} %Y/%m/%d %c"
# Setup screens
screen -t 'main' 0 bash # Make first screen - main
screen -t 'test'  1 bash # Make screen - test
# Switch to screen 1
select 1
```
2. Start the screen
`screen -RD`
(Detach from the screen with " Ctrl+a d")

3. Check `screen -ls`

4. Close terminal, open new one and return back:

`screen -RD`


> There is also `-d -m` switch combination to start  screen  in  "detached" mode, i.e. create a new session but don't attach to it. It’s useful for running scripts.

> Another alternative programs are: `tmux` 
> http://habrahabr.ru/post/126996/

