# Essentials

- [Essentials](#essentials)
  - [output-redirection](#output-redirection)
  - [input-redirection](#input-redirection)
  - [eof-here-document](#eof-here-document)
  - [dev-null-and-others-usually-used-in-redirection](#dev-null-and-others-usually-used-in-redirection)
  - [special-variables](#special-variables)
    - [#! /Shebang character](#-shebang-character)
    - [Variables](#variables)
    - [Command line Arguments](#command-line-arguments)
  - [special-files](#special-files)
    - [/etc/fstab](#etcfstab)
    - [/proc/sys/fs/file-max vs ulimit](#procsysfsfile-max-vs-ulimit)

## output-redirection

The output from a command normally intended for standard output can be easily diverted to a file instead. This capability is known as output redirection.

If the notation > file is appended to any command that normally writes its output to standard output, the output of that command will be written to file instead of your terminal.

Check the following who command which redirects the complete output of the command in the users file.

```bash
$ who > users
$ cat users
oko         tty01 Sep 12 07:30
ai          tty15 Sep 12 13:32
ruth        tty21 Sep 12 10:10
pat         tty24 Sep 12 13:07
steve       tty25 Sep 12 13:03
$
```

If a command has its output redirected to a file and the file already contains some data, that data will be lost.

You can use >> operator to append the output in an existing file

## input-redirection

The commands that normally take their input from the standard input can have their input redirected from a file in this manner. For example, to count the number of lines in the file users generated above, you can execute the command as follows −

```bash
$ wc -l users
2 users
$
```

Upon execution, you will receive the following output. You can count the number of lines in the file by redirecting the standard input of the wc command from the file users −

```bash
$ wc -l < users
2
$
```

In the first case, wc knows that it is reading its input from the file users. In the second case, it only knows that it is reading its input from standard input so it does not display file name.

## eof-here-document

A here document is used to redirect input into an interactive shell script or program.

We can run an interactive program within a shell script without user action by supplying the required input for the interactive program, or interactive shell script.

The general form for a here document is −

```bash
command << delimiter
document
delimiter
```

Here the shell interprets the << operator as an instruction to read input until it finds a line containing the specified delimiter. All the input lines up to the line containing the delimiter are then fed into the standard input of the command.

The delimiter tells the shell that the here document has completed. Without it, the shell continues to read the input forever. The delimiter must be a single word that does not contain spaces or tabs.

Following is the input to the command wc -l to count the total number of lines −

```bash
$wc -l << EOF
   This is a simple lookup program 
for good (and bad) restaurants
in Cape Town.
EOF
3
$
```

## dev-null-and-others-usually-used-in-redirection

**>/dev/null 2>&1 meaning**

```bash
x * * * * /path/to/my/script > /dev/null 2>&1
```

- \> is for redirect
- /dev/null is a black hole where any data sent, will be discarded
- 2 is the file descriptor for Standard Error
- \> is for redirect
- & is the symbol for file descriptor (without it, the following 1 would be considered a filename)
- 1 is the file descriptor for Standard Out
- Therefore >/dev/null 2>&1 is redirect the output of your program to /dev/null. Include both the Standard Error and Standard Out.

**>, 1>, 2> meaning**

The > operator redirects the output usually to a file but it can be to a device. You can also use >> to append.

If you don't specify a number then the standard output stream is assumed but you can also redirect errors

- \> file redirects stdout to file
- 1> file redirects stdout to file
- 2> file redirects stderr to file
- &> file redirects stdout and stderr to file

/dev/null is the null device it takes any input you want and throws it away. It can be used to suppress any output.

**3>&1 1>&2 2>&3 meaning**

\>name means redirect output to file name.

\>&number means redirect output to file descriptor number.

The numbers are file descriptors and only the first three (starting with zero) have a standardized meaning:

- 0 - stdin
- 1 - stdout
- 2 - stderr

So each of these numbers in your command refer to a file descriptor. You can either redirect a file descriptor to a file with > or redirect it to another file descriptor with >&

The 3>&1 in your command line will create a new file descriptor and redirect it to 1 which is STDOUT. Now 1>&2 will redirect the file descriptor 1 to STDERR and 2>&3 will redirect file descriptor 2 to 3 which is STDOUT.

So basically you switched STDOUT and STDERR, these are the steps:

- Create a new fd 3 and point it to the fd 1
- Redirect file descriptor 1 to file descriptor 2. If we wouldn't have saved the file descriptor in 3 we would lose the target.
- Redirect file descriptor 2 to file descriptor 3. Now file descriptors one and two are switched.

Now if the program prints something to the file descriptor 1, it will be printed to the file descriptor 2 and vice versa.

**Difference between < and << in shell command**

\> is used to write to a file and >> is used to append to a file.

Thus, when you use ps aux > file, the output of ps aux will be written to file and if a file named file was already present, its contents will be overwritten.

And if you use ps aux >> file, the output of ps aux will be written to file and if the file named file was already present, the file will now contain its previous contents and also the contents of ps aux, written after its older contents of file.

**Difference between single and double quotes in Bash ( "" and '')**

Single quotes won't interpolate anything, but double quotes will. For example: variables, backticks, certain \ escapes, etc

The Bash manual has this to say:

SINGLE QUOTES

- Enclosing characters in single quotes (') preserves the literal value of each character within the quotes. A single quote may not occur between single quotes, even when preceded by a backslash.

DOUBLE QUOTES

- Enclosing characters in double quotes (") preserves the literal value of all characters within the quotes, with the exception of $, `, \, and, when history expansion is enabled, !. The characters $ and ` retain their special meaning within double quotes (see Shell Expansions). The backslash retains its special meaning only when followed by one of the following characters: $, `, ", \, or newline. Within double quotes, backslashes that are followed by one of these characters are removed. Backslashes preceding characters without a special meaning are left unmodified. A double quote may be quoted within double quotes by preceding it with a backslash. If enabled, history expansion will be performed unless an ! appearing in double quotes is escaped using a backslash. The backslash preceding the ! is not removed. The special parameters * and @ have special meaning when in double quotes

## special-variables

### #! /Shebang character

In computing, a shebang is the character sequence consisting of the characters number sign and exclamation mark (#!) at the beginning of a script.

In Unix-like operating systems, when a text file with a shebang is used as if it is an executable, the program loader parses the rest of the file's initial line as an interpreter directive; the specified interpreter program is executed, passing to it as an argument the path that was initially used when attempting to run the script,[8] so that the program may use the file as input data. For example, if a script is named with the path path/to/script, and it starts with the following line, #!/bin/sh, then the program loader is instructed to run the program /bin/sh, passing path/to/script as the first argument

SYNTAX

The form of a shebang interpreter directive is as follows:

> \#!interpreter [optional-arg]

- in which interpreter is an absolute path to an executable program. The optional argument is a string representing a single argument. White space after #! is optional. In Linux, the file specified by interpreter can be executed if it has the execute right and contains code which the kernel can execute directly, if it has a wrapper defined for it via sysctl (such as for executing Microsoft EXE binaries using wine), or if it contains a shebang.
- Some other example shebangs are:

```bash
#!/bin/sh — Execute the file using sh, the Bourne shell, or a compatible shell 
#!/bin/csh — Execute the file using csh, the C shell, or a compatible shell 
#!/usr/bin/perl -T — Execute using Perl with the option for taint checks 
#!/usr/bin/php — Execute the file using the PHP command line interpreter 
#!/usr/bin/python -O — Execute using Python with optimizations to code 
#!/usr/bin/ruby — Execute using Ruby 
```

### Variables

The following shows a number of special variables that you can use in your shell scripts −

$0

- The filename of the current script

$n

- These variables correspond to the arguments with which a script was invoked. Here n is a positive decimal number corresponding to the position of an argument (the first argument is $1, the second argument is $2, and so on).

$#

-The number of arguments supplied to a script

$*

- All the arguments are double quoted. If a script receives two arguments, $* is equivalent to $1 $2

$@

- All the arguments are individually double quoted. If a script receives two arguments, $@ is equivalent to $1 $2

$?

- The exit status of the last command executed. Exit status is a numerical value returned by every command upon its completion. As a rule, most commands return an exit status of 0 if they were successful, and 1 if they were unsuccessful.

$$

- The process number of the current shell. For shell scripts, this is the process ID under which they are executing.

$!

- The process number of the last background command.

### Command line Arguments

The command-line arguments $1, $2, $3, ...$9 are positional parameters, with $0 pointing to the actual command, program, shell script, or function and $1, $2, $3, ...$9 as the arguments to the command.

Following script uses various special variables related to the command line −

```bash
#!/bin/sh
echo "File Name: $0"
echo "First Parameter : $1"
echo "Second Parameter : $2"
echo "Quoted Values: $@"
echo "Quoted Values: $*"
echo "Total Number of Parameters : $#"
```

Here is the output of the following script

```bash
$./test.sh Zara Ali
File Name : ./test.sh
First Parameter : Zara
Second Parameter : Ali
Quoted Values: Zara Ali
Quoted Values: Zara Ali
Total Number of Parameters : 2
```

## special-files

### /etc/fstab

[http://www.linfo.org/etc_fstab.html](http://www.linfo.org/etc_fstab.html)

fstab is a system configuration file on Linux and other Unix-like operating systems that contains information about major filesystems on the system. It takes its name from file systems table, and it is located in the /etc directory.

/etc/fstab can be safely viewed by using the cat command (which is used to read text files) as follows:

```bash
#
# /etc/fstab
# Created by anaconda on Mon Aug 14 20:57:26 2017
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
UUID=784a2acb-e8a7-4485-a6f6-6c2333d013b1 /                       xfs defaults 0 0
/dev/vdb /mnt auto defaults,nofail,comment=cloudconfig 0 2
dfw-nfs3-vs10.prod.ankit.com:/stg_ankit_geo_01 /geo_camel_nfsshared/  nfs defaults 0 0
```





### /proc/sys/fs/file-max vs ulimit

file-max is the maximum File Descriptors (FD) enforced on a kernel level, which cannot be surpassed by all processes without increasing.

The ulimit is enforced on a process level, which can be less than the file-max. If some user has launched 4 processes and the ulimit configuration for FDs is 1024, each process may open 1024 FDs. The user is not going to be limited to 1024 FDs but the processes which are launched by that user.

```bash
me@superme:~$ ulimit -n 
1024 
me@superme:~$ lsof | grep $USER | wc -l 
8145
```

How do I know if I’m getting close to hitting this limit on my server? Run the command: cat /proc/sys/fs/file-nr. This will return three values, denote the number of allocated file handles, the number of allocated but unused file handles, and the maximum number of file handles. Note that file-nr IS NOT a tunable parameter. It is informational only. On my server, this returns: 3488 0 793759. This means that currently, my server has only allocated 3488 of the 793,759 allocation limit and is in no danger of hitting this limit at this time.




