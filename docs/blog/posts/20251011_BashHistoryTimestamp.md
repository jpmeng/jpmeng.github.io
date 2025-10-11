---
date: 2025-10-11
tags: [Linux, Bash, history]
title: Timestamp in Bash history
summary: Tune the Bash history to show the timestamp and distinguish host
categories: [Linux]
---

# Timestamp in Bash history

On Linux systems—especially in cluster environments—it’s often necessary to review the history of commands that have been executed. By default, however, Bash does not display timestamps for commands in history.

To enable timestamps, set the `HISTTIMEFORMAT` environment variable in the `.bashrc` file.

<!-- more -->

Below is an example section of a `.bashrc` file that configures Bash to record and display timestamps in command history. Line 2 allows each node or server to have its own history file, which is particularly useful in cluster environments where multiple nodes share the same storage. Lines 11-14 ensure that the records are synchronized if multiple sessions, e.g., tmux or screen, are running at the same time.

```bash  linenums="1"
#Set the history file according to hostname
export HISTFILE="$HOME/.bash_history_$(hostname -s)"
#Show the timestamp as day-month-year time
export HISTTIMEFORMAT="%d/%m/%y %T "
#Set the number of commands recorded in the memory
export HISTSIZE=1000
#Set the number of command recorded in the file specified by HISTFILE
export HISTFILESIZE=10000
#Store multi-line commands in one history entry
shopt -s cmdhist
#Append commands to the history
shopt -s histappend
#Save each command right after it has been executed.
export PROMPT_COMMAND="history -a;history -n"
```
