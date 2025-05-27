# Linux Administration and Networking Basics (level 2) <br /> Linux-ի կառավարում և ցանցային հիմունքներ (փուլ 2)

## tmux & screen

### Tmux

`tmux` is terminal multiplexer

It acts as a remote terminal session manager that multiplexes a single connection between several windows (typically interactive shells). 
It provides the detaching/reattaching functionality.


Each process gets its own virtual window, and you can switch between virtual windows interacting with each process. 
The processes managed by `tmux` continue to run when their window is not active. 
`tmux` offers the ability to detach (Ctrl+b d) from a session 
and then attach to it at a later time (`tmux a`)

When detached from a session, the processes `tmux` is managing continue to run. 
You can then re-attach to the session at a later time with `tmux a`, 
and your terminals are still there, the way you left them.


Good way to runs tmux is:
```bash
tmux a || tmux
```
This way you try to connect to existing session if not create new one.

#### PRACTICE
Create an alias `tm` for above command and make it active after re-login.


Keys bindings in `tmux`:
```bash

Ctrl+b d  - detach from terminal.	Run `tmux a` to reattach

Windows:
Ctrl+b c  - create new window
Ctrl+b p  - previous window
Ctrl+b n  - next window

Panes: 
Ctrl+b "  - create new vertical pane
Ctrl+b %  - create new horizontal pane
Ctrl+b arrow (up/down/left/right)  - switch to pane in that direction
Ctrl+b o  - next pane ( or above arrows )

Ctrl+b x  - close current pane/window (instead you can just type `exit` in that terminal) 
```

#### Scrolling in tmux

One important problem with using `tmux` is related to scrolling up. 

In order to do that you need to enter special **copy mode** with `Ctrl+b PgUp` 
(in fact it will enter that mode and scroll one page up.
If you want to only enter that mode press `Ctrl+b [` instead).

After that you can use `PgUp`/`PgDn` or `Up arrow`/`Down arrow` keys for scrolling.

To exit **copy mode** press `q`

#### Changing hotkey
You can change hotkey from `Ctrl+b` to `Ctrl+a`

Create `~/.tmux.conf` and add following to it:

```bash
# Change prefix key to Ctrl+a
set -g prefix C-a
```

#### Mouse support

Mouse support for `tmux` can be enabled by adding following to `~/.tmux.conf`:

```bash
# Enable history scroll, selecting of window/pane and pane resize with mouse
set -g mouse on
```

Now you should be able to you mouse to:
* select pane by clicking inside it.
* select window by clicking its name at bottom status line.
* resize pane by dragging borders.
* scroll up in window or pane.


The above settings can also be enabled in the tmux via **command mode** (key to enter command mode is `:`) 
For example to change prefix key type `Ctrl+b :` and type: 
`set -g prefix C-a`





> For more info see `man tmux`
> or sites like: 
> https://tmuxcheatsheet.com

### Screen
`screen`  is another terminal multiplexer

It acts the same way like `tmux` and is considered older solution. So it is now better to use `tmux`.


`screen -RD`


Each process gets its own virtual window, and you can bounce between virtual windows interacting with each process. The processes managed by screen continue to run when their window is not active. Screen offers the ability to detach from a session and then attach to it at a later time. When detached from a session, the processes screen is managing continue to run. You can then re-attach to the session at a later time, and your terminals are still there, the way you left them.

Before actually running screen, it's important to understand how to interact with it. Screen sends all entered text to the current window, with the exception of the command character. The default command character is Ctrl-a (press the Ctrl and the a key at the same time).
The command character is used to notify screen that you'd like to control screen itself, rather than the application in the current window. The key pressed after the command character designates which screen command you would like to perform. Some of the more useful commands and their key bindings are shown in table below:
Keys bindings in screen:
```bash
Ctrl+a c	new window
Ctrl+a n	next window
Ctrl+a p	previous window
Ctrl+a d	detach screen from terminal.	Run 'screen -RD' to reattach
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


