# Giving the Terminal Color
To ease navigating within a command line interface, you can add color to the terminal prompt, folders, and files so that your eye can process information quickly.

### 1. You can add the following command to the end of your __~/.bashrc__ file:

You can use [vim](https://www.openvim.com/) to add the line into the __~/.bashrc__ file.

```
PS1='\[\033[1;36m\]\u\[\033[1;31m\]@\[\033[1;32m\]\h:\[\033[1;35m\]\w\[\033[1;31m\]\$\[\033[0m\] '
```

Your __~/.bashrc__ file would look something like this:

```
# .bashrc

# Source global definitions
if [ -f /etc/bashrc ]; then
        . /etc/bashrc
fi

# Uncomment the following line if you don't like systemctl's auto-paging feature:
# export SYSTEMD_PAGER=

# User specific aliases and functions
PS1='\[\033[1;36m\]\u\[\033[1;31m\]@\[\033[1;32m\]\h:\[\033[1;35m\]\w\[\033[1;31m\]\$\[\033[0m\] '
```

### 2. Apply effects
To add the effect refresh your terminal with the new __~/.bashrc__ file:
```
source ~/.bashrc
```
