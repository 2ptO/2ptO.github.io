# My Linux CLI and bash tricks

- [My Linux CLI and bash tricks](#my-linux-cli-and-bash-tricks)
    - [Basics](#basics)
    - [Commands](#commands)
        - [Access the last executed command](#access-the-last-executed-command)
        - [Access arguments from last executed command](#access-arguments-from-last-executed-command)
        - [ls tricks](#ls-tricks)
        - [grep tricks](#grep-tricks)
        - [Navigating within a command](#navigating-within-a-command)
    - [Shell](#shell)
        - [Change shell prompt](#change-shell-prompt)
        - [Set environment variables](#set-environment-variables)
    - [Navigation](#navigation)
        - [CD PATH](#cd-path)
        - [Back to previous dir](#back-to-previous-dir)
        - [Copy/Paste File](#copypaste-file)
    - [File ops](#file-ops)
    - [SSH](#ssh)
    - [Up next](#up-next)
    - [Linux Daily](#linux-daily)
        - [Devices](#devices)
    - [Vim tips](#vim-tips)
        - [How to associate new file types with existing file types](#how-to-associate-new-file-types-with-existing-file-types)
        - [How to delete all lines in a file](#how-to-delete-all-lines-in-a-file)
        - [Keyboard shortcuts](#keyboard-shortcuts)

## Basics

1. Find which shell I'm currently in. **`echo $SHELL`**
2. Print OS details with **`uname`** command.

```shell
user01@user01-mac-1:~$ uname -a
Darwin user01-mac-1 18.6.0 Darwin Kernel Version 18.6.0: Thu Apr 15 23:16:27 PDT 2019; root:xnu-4903.261.4~2/RELEASE_X86_64 x86_64
```

3. Search for system commands using **`apropos`**

```text
user01@user01-1604:~$apropos batch
at(1), batch(1), atq(1), atrm(1) - queue, examine, or delete jobs for later execution
```

4. Find disk usage of a directory and its files using **`du`** command.
Some frequently used options:

**-a** - display an entry for each file in the directory
**-c** - display a grand total
**-h** - display info in human readable form (take optional unit suffixes (-m for MB, -g for GB))

```text
user01@user01-mac-1:temp$ du -ach
1.0G	./file2.dat
1.0G	./file1.dat
2.0G	.
2.0G	total
```

## Commands

### Access the last executed command

Often come in handy to run sudo when we forget to include in sudo in the first invocation

```text
user01@user01-1604:~$ cp /etc/iscsi/initiatorname.iscsi .
cp: cannot open '/etc/iscsi/initiatorname.iscsi' for reading: Permission denied
user01@user01-1604:~$ sudo !!
sudo cp /etc/iscsi/initiatorname.iscsi .
[sudo] password for user01:
user01@user01-1604:~$
```

### Access arguments from last executed command

- Print return status of last executed command - `echo $?`
- Use arguments from previous command
    - First argument - `!^`
    - Last argument - `!$`
    - All arguments - `!*`

```shell
mkdir mytemp
cd !*
touch a b c d
# delete only a
rm $^
ls b c d
# remove d
rm !$
ls b c
# remove all
rm !*
```

### ls tricks

- Always turn on coloring - `ls --color=always`. On macOS, use `ls -G`
- ll - shortcut to `ls -l`. not available on all distributions though. I don't see it macOS.
- `ls -li` lists the inode number of the files. But we rarely deal with inode numbers in day to day ops. Just noting it here for future reference
- `ls -lash` - show a **long** listing of **all** files along with their **actual file size in number of blocks** (typically 512 bytes) and file size in **human readable** form (e.g B for bytes, K for kilobytes, G for gigabytes etc)

```text
guest@temp $ ls -lash
total 32
0 drwxr-xr-x   6 deepan  staff   192B Aug 27 10:57 .
0 drwx------+ 86 deepan  staff   2.7K Aug 27 10:56 ..
8 -rw-r--r--   1 deepan  staff    40B Aug 27 10:58 a
8 -rw-r--r--   1 deepan  staff    40B Aug 27 10:58 b
8 -rw-r--r--   1 deepan  staff    40B Aug 27 10:58 c
8 -rw-r--r--   1 deepan  staff    40B Aug 27 10:58 d
```

### grep tricks

- Always turn on coloring - `grep --color-always` and `egrep --color=always`
- I often pipe the grep output to `less` to paginate. With that, coloring enabled from grep output will be lost with default execution of less. Use `less -R` to turn on raw processing of ANSI colors in the input.
- Display file name and numbers of line matching a given pattern with grep
    - `-n` - display line number
    - `-H` - display file name

### Navigating within a command

| **Command**  | **Description**                                               |
| ------------ | ------------------------------------------------------------- |
| **Ctrl + A** | Go to beginning of the line                                   |
| **Ctrl + E** | Go to end of the line                                         |
| **Ctrl + U** | Clear the line(and copy the cleared contents to paste buffer) |
| **Esc + f**  | Go forward one word                                           |
| **Esc + b**  | Go back one word                                              |

## Shell

### Change shell prompt

Prompt strings are controlled by the primary environment variable PS1. Change the value of `PS1` to get the desired prompt string. There is lot of special characters available to customize the prompt string with different information.

e.g. this prompt string shows `host:current_working_directory username$`

```shell
guest@~ $ PS1="\h:\W \u\$ "
```

The list of special characters as mentioned in the bash man page:

```text
PROMPTING
       When executing interactively, bash displays the primary prompt PS1 when
       it is ready to read a command, and the secondary  prompt  PS2  when  it
       needs  more  input  to  complete  a  command.  Bash allows these prompt
       strings to be customized by inserting  a  number  of  backslash-escaped
       special characters that are decoded as follows:
              \a     an ASCII bell character (07)
              \d     the  date  in "Weekday Month Date" format (e.g., "Tue May
                     26")
              \D{format}
                     the format is passed to strftime(3)  and  the  result  is
                     inserted  into the prompt string; an empty format results
                     in a locale-specific time representation.  The braces are
                     required
              \e     an ASCII escape character (033)
              \h     the hostname up to the first `.'
              \H     the hostname
              \j     the number of jobs currently managed by the shell
              \l     the basename of the shell's terminal device name
              \n     newline
              \r     carriage return
              \s     the  name  of  the shell, the basename of $0 (the portion
                     following the final slash)
              \t     the current time in 24-hour HH:MM:SS format
              \T     the current time in 12-hour HH:MM:SS format
              \@     the current time in 12-hour am/pm format
              \A     the current time in 24-hour HH:MM format
              \u     the username of the current user
              \v     the version of bash (e.g., 2.00)
              \V     the release of bash, version + patch level (e.g., 2.00.0)
              \w     the  current  working  directory,  with $HOME abbreviated
                     with a tilde
              \W     the basename of the current working directory, with $HOME
                     abbreviated with a tilde
              \!     the history number of this command
              \#     the command number of this command
              \$     if the effective UID is 0, a #, otherwise a $
              \nnn   the character corresponding to the octal number nnn
              \\     a backslash
              \[     begin  a sequence of non-printing characters, which could
                     be used to embed a terminal  control  sequence  into  the
                     prompt
              \]     end a sequence of non-printing characters

       The  command  number  and the history number are usually different: the
       history number of a command is its position in the history list,  which
       may  include  commands  restored  from  the  history  file (see HISTORY
       below), while the command number is the position  in  the  sequence  of
       commands  executed  during the current shell session.  After the string
       is decoded, it is expanded via parameter expansion,  command  substitu-
       tion,  arithmetic expansion, and quote removal, subject to the value of
       the promptvars shell option (see the description of the  shopt  command
       under SHELL BUILTIN COMMANDS below).
```

- [ ] Read later [this](https://linuxconfig.org/bash-prompt-basics) link

### Set environment variables

With Bash shell, environment variables can be set in two ways:

1. $ VAR=VALUE
   e.g `guest@~ $ LOG_DIR="/var/log"`
2. $ export VAR=VALUE
   e.g. `guest@~ $ export LOG_DIR="/var/log"`

Quotes around the values are normally not needed unless the value contains spaces. Exporting an environment variable makes it available to the subprocess as well.

Environment variables can be read using `echo $VAR` or `printenv`. With `echo`, shell offers tab completion as well. `printenv` lists all the environment variables, hence grepping is needed sometimes to filter.

```shell
guest@~ $ echo $LOG_DIR
/var/log
guest@~ $ printenv | grep LOG_DIR
LOG_DIR=/var/log
```

## Navigation

### CD PATH

- Some times the most commonly used directories might be buried under multiple levels. It will be cumbersome to remember and include the full path each time. Just like PATH, we can include a set of directories to be searched when we run cd command. That way we can simply cd into the required directory without remembering the full path.

```bash
# the following two directories (homedir and user01's workspace directory) will be searched on each invocation of 'cd' if the first attempt fails.
export CDPATH='~:/home/users/user01/workspace/:`
```

### Back to previous dir

`cd -` to get back to the directory from where you cd into.

### Copy/Paste File

To take a copy of a file, I used to use this command. `cp filename.ext filename.ext.bkup`. There is a nifty shortcut in bash that we can use to avoid typing the filename again.

```unix
$cp filename.ext{,.bkup}
```

## File ops

1. Get file status (e.g. access time, size, permissions etc.) in detail with **`stat`** command.
    1. Use option `-x` to get verbose output
    2. Use option `-s` to get shell like output for easy string processing

```text
$ ls
a	b

$ stat -x a
  File: "a"
  Size: 278          FileType: Regular File
  Mode: (0644/-rw-r--r--)         Uid: (  501/  deepan)  Gid: (   20/   staff)
Device: 1,4   Inode: 4308859545    Links: 1
Access: Mon Aug 12 13:28:35 2019
Modify: Mon Aug 12 13:28:22 2019
Change: Mon Aug 12 13:28:22 2019

$ stat -s a
st_dev=16777220 st_ino=4308859545 st_mode=0100644 st_nlink=1 st_uid=501 st_gid=20 st_rdev=0 st_size=278 st_atime=1565630915 st_mtime=1565630902 st_ctime=1565630902 st_birthtime=1565364057 st_blksize=4096 st_blocks=8 st_flags=0
```

2. Create large files - I often need simple large files for testing some of my programs. A quick way to create large files is using **`dd`** command

e.g. `dd if=/dev/zero of=/home/users01/large_file bs=1M count=5000` - creates a file of size 5G filled with zeroes.

3. Add line numbers to inputs and write to standard output with **`nl`** command

```text
$ls
a  b  c  d  e  f
$ll | nl
     1  total 0
     2  -rw-r--r-- 1 deepan engr 0 Aug 14 10:52 a
     3  -rw-r--r-- 1 deepan engr 0 Aug 14 10:52 b
     4  -rw-r--r-- 1 deepan engr 0 Aug 14 10:52 c
```

4. Pipe output to multiple files with tee. This comes in handy when we have write some logs to multiple files in parallel.
Here, we have a bunch of empty files. The option `-a` to `tee` command appends the output to the given files. The writes to the files are **unbufferred**

```text
guest@temp $ ls -l
total 0
-rw-r--r--  1 deepan  staff  0 Aug 27 10:57 a
-rw-r--r--  1 deepan  staff  0 Aug 27 10:57 b
-rw-r--r--  1 deepan  staff  0 Aug 27 10:57 c
-rw-r--r--  1 deepan  staff  0 Aug 27 10:57 d
guest@temp $ echo "The quick brown fox jumps over lazy dog" | tee -a a b c d
The quick brown fox jumps over lazy dog
guest@temp $ ls -l
total 32
-rw-r--r--  1 deepan  staff  40 Aug 27 10:58 a
-rw-r--r--  1 deepan  staff  40 Aug 27 10:58 b
-rw-r--r--  1 deepan  staff  40 Aug 27 10:58 c
-rw-r--r--  1 deepan  staff  40 Aug 27 10:58 d
guest@temp $ cat a b c d
The quick brown fox jumps over lazy dog
The quick brown fox jumps over lazy dog
The quick brown fox jumps over lazy dog
The quick brown fox jumps over lazy dog
```

5. tail - this comes in more handy than `head` command. The default behavior is to list last 10 lines in a file (or piped inputs too). We can also use this to skip the first 'n' number of lines. Check out `tail --help` for more options.

```bash
# show the last 10 lines
tail somefile.txt

# show the last 10 lines
tail -n 10 somefile.txt

# start at 10th line in the given file
tail -n +10 somefile.txt
```

## SSH

Learnt about the ssh_config recently. Comes in very handy to specify host specific options.

- Create `~/.ssh/config` file if it doesn't exist
- Contents of `~/ssh/config` must follow the defined ordering (See config file details [here](https://www.ssh.com/ssh/config/)). It generally goes like this

```ssh_config
Host <hostname>
    SSH_OPTION_NAME OPTION_VALUE
    SSH_OPTION_NAME OPTION_VALUE
    SSH_OPTION_NAME OPTION_VALUE
    .
    .
```

- `<hostname>` can be a keyword or regex to match multiple hosts
- Option names available for the system can be seen with `man ssh_config` command
- I normally connect to multiple VMs at work, but those VMs are frequently reinstalled for various reasons. To avoid the hassle with known_hosts file, I use the following options with some machines that I connect to and also run screen upon logging.
- Some relevant links: [linuxize](https://linuxize.com/post/using-the-ssh-config-file/)
  
```ssh_config
Host cr6
        HostName host1.eng.aws.com
        StrictHostKeyChecking no
        UserKnownHostsFile /dev/null
        # Run these commands on the remote machine upon logging in. Equivalent to specifying the command in 'ssh host <commands>' itself.
        RemoteCommand screen -ls;screen -Dr;
        # Always request a pseudo terminal. Otherwise, ssh session
        # will exit after running the commands in RemoteCommand
        RequestTTY yes
```

## Up next

- [x] `service` command. System V Init Scripts.
    - Turns out this is little more advanced. *System V* is a mode of managing the init scripts that is widely ued. It is also aging and is replaced by newer versions. Some other implementations are *Upstart*, *systemd* and *launchd*
    - System V looks for init scripts in */etc/rc.d/*. There are different run levels defined and each run level may have multiple scripts. The scripts are placed under `/etc/rc.d/rc<runlevel>.d` directory.
    - I think *upstart* was developed by Canonical and has been used in Ubuntu and some other variants of linux.
    - macOS uses *launchd* to startup and shutdown services during init and shutdown. Upon boot, *launchd* looks up */Library/LaunchDaemons/*  for daemons to start. The *plist* files determine which daemon to run and the arguments as well.
    - In searching for material about System V, I stumbled upon [LinuxJourney](https://linuxjourney.com/lesson/sysv-overview). Found it very organized and useful.

## Linux Daily

- [Basics of FileSystem IO](https://medium.com/databasss/on-disk-io-part-1-flavours-of-io-8e1ace1de017)
    - different flavors of IO (syscall, standard IO, memory mapped, vectored IO)
    - Standard IO uses read() and write() system calls, takes advantage of page cache. Dirty pages flushed on to disk during fsync().
    - Importance of buffer cache (aka page cache)
    - write back vs write through
    - O_DIRECT flag (enforces write through, ignores operating system page cache optimizations)
    - block alignment

### Devices

- Find them in **/dev** directory.
- common types of devices
    - *Block* - e.g. disk drives, flash drives
    - *Character* - e.g. terminal, pseudo devices (*/dev/zero*, */dev/null*, */dev/random*)
    - *Pipe*
    - *Socket*
- **/dev** has its own file system named **devfs**
- List all block devices connected to the system with `lsblk` command. This command is not available in macOS. Use `diskutil info -a` instead

## Vim tips

- Turn on syntax highlighting - `:syntax on`
- Set coloring scheme - `:colorscheme pablo`. Tab after :colorscheme to cycle through available color schemes (listed under `/usr/share/vim/vim80/colors/`)
- Expand tabs to spaces while pressing tab key - `:expandtab`
    - Set the tabstop and shiftwidth - `:set tabstop=4 shiftwidth=4`. Some people prefer to use 8 spaces for tabs and some prefer to use 4 or even 2. I commonly use 8 for C files and 4 for other languages
- Convert existing tabs into spaces - `:retab`

### How to associate new file types with existing file types

I often work with some unit test files that are typically maintained with `.ut` extension, but the code as such is written in C++. So I was looking for ways to open these `.ut` files in vim, but treat them like c++ file. I stumbled upon this [page](https://codeyarns.github.io/tech/2015-03-19-how-to-associate-files-with-existing-filetype-in-vim.html) recently. I added this line to my `.vimrc` file and totally got me what I wanted.

```text
autocmd BufRead,BufNewFile *.foo set filetype=html
```

### How to delete all lines in a file

I don't run into this situation often, but when I need to do so, I follow these steps in the command mode: 

1. Press `gg` // moves the cursor to the beginning of the file
2. Press `dG`// delete all content from the current position of the cursor to the end of the file

Alternatively, we can do this by finding the total number of lines in the file and then press `<total-number-of-lines>` followed by `dd` to delete that many lines.

### Keyboard shortcuts

| Key    | Operation                                                          |
| ------ | ------------------------------------------------------------------ |
| **d0** | Delete chars from current cursor position to beginning of the line |
| **d$** | Delete chars from current cursor position to end of the line       |
| **dG** | Delete chars from current cursor position to the end of the file   |
