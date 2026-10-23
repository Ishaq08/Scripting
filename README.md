# Shell Scripting: Lecture 1 Notes

A summary of everything covered in the first Shell Scripting lecture (bash).

## Contents

1. [What is shell scripting?](#1-what-is-shell-scripting)
2. [Shell vs bash](#2-shell-vs-bash)
3. [Running a script](#3-running-a-script)
4. [Variables](#4-variables)
5. [env vs set](#5-env-vs-set)
6. [Calling one script from another (source)](#6-calling-one-script-from-another-source)
7. [Running multiple commands](#7-running-multiple-commands)
8. [Math with expr](#8-math-with-expr)
9. [Quotes](#9-quotes)
10. [sum.sh and read](#10-sumsh-and-read)
11. [name.sh and system report](#11-namesh-and-system-report)
12. [Common mistakes](#12-common-mistakes)

---

## 1. What is shell scripting?

A shell script is a text file with a `.sh` extension that holds a list of commands. Instead of typing commands one by one, you run the file and the shell executes them in order.

Every script starts with a **shebang** line that tells the system which shell to use:

```bash
#!/bin/bash
```

First script:

```bash
#!/bin/bash
echo "Hello, welcome to shell scripting"
date
```

## 2. Shell vs bash

**Shell** is the category: any program that reads your commands and runs them. **Bash** (Bourne Again Shell) is one specific shell, and the default on most Linux systems.

| Shell | Name | Notes |
|---|---|---|
| `sh` | Bourne Shell | The original, few features |
| `bash` | Bourne Again Shell | Improved `sh`, default on most Linux |
| `zsh` | Z Shell | Default on macOS |
| `ksh` | Korn Shell | Older Unix systems |

Bash adds history, tab completion, arrays, `[[ ]]` and `$( )`. A script using bash-only features may fail with `sh file.sh`, so use `#!/bin/bash` and run with `bash file.sh`.

Check your shell: `echo $SHELL`

## 3. Running a script

```bash
bash file.sh
sh file.sh
```

These two need execute permission first:

```bash
chmod +x file.sh
./file.sh
/full/path/file.sh
```

## 4. Variables

A variable stores a value as **key = value**.

```bash
name=amir
echo $name
echo "my name is $name"
```

Rules:
- **No spaces** around `=` (`name = amir` is an error).
- Read the value with `$name`.

### Types

| Type | Meaning | Written in | See with |
|---|---|---|---|
| SDV (system defined) | Already exist in the system | CAPITAL letters | `env` / `printenv` |
| UDV (user defined) | Created by you | capital or small | `set` |

### SDV lab

```bash
echo $USER      # username
echo $HOME      # home directory
echo $UID       # user ID
echo $PWD       # current directory
echo $HISTSIZE  # commands kept in history
umask           # note: UMASK is not a variable, use the command
env
printenv
```

### UDV lab

```bash
x=10
y=20
echo "value of x is = $x"
echo "value of y is = $y"
```

`hello.sh`:

```bash
#!/bin/bash
x=amir
echo "your name is $x"
```

Run: `bash hello.sh`

## 5. env vs set

A variable created with `x=10` is **local** to the current shell.

```bash
x=10
env | grep "^x="       # prints nothing
set | grep "^x="       # prints x=10

export x=10
env | grep "^x="       # now prints x=10
```

| Command | Shows |
|---|---|
| `env` / `printenv` | exported variables only |
| `set` | all variables, including your own |

`grep` filters output. `|` (pipe) sends one command's output into the next.

## 6. Calling one script from another (source)

`hello.sh` (from above), then `demo.sh`:

```bash
#!/bin/bash
echo "welcome to devops"
source /home/ishaq/hello.sh
echo ".........task done........"
```

Output:

```
welcome to devops
your name is amir
.........task done........
```

Short form: `. /home/ishaq/hello.sh`

| Method | Runs in | Variables kept? |
|---|---|---|
| `source hello.sh` | same shell | yes |
| `bash hello.sh` | separate shell | no |

## 7. Running multiple commands

`commands.sh`:

```bash
#!/bin/bash
echo "THIS script runs multiple commands"
date
cal
timedatectl
hostname -I
uptime
```

| Command | Shows |
|---|---|
| `date` | current date and time |
| `cal` | calendar of this month |
| `timedatectl` | time, time zone, clock settings |
| `hostname -I` | IP addresses of the computer |
| `uptime` | how long the system has been running |

Notes:
- Use `hostname -I`, not `hostnamectl -I` (that option does not exist).
- If a command fails, the script continues with the next one.
- If `cal` is missing: `sudo apt install ncal`
- `hostname -I` lists all IPs: the main one (e.g. `192.168.x.x`), virtual ones (Docker, VMs), and IPv6 ones (with colons).

## 8. Math with expr

Variables hold text by default, so use `expr` for math. Put a **space on both sides** of every operator.

```bash
expr 20 + 50       # 70
expr 20 - 5        # 15
expr 10 / 2        # 5
expr 17 % 5        # 2

a=10
b=20
expr $a + $b       # 30

x=4
y=12
expr $x \* $y      # 48 (multiplication needs a backslash)
```

| Operator | Meaning |
|---|---|
| `+` | addition |
| `-` | subtraction |
| `\*` | multiplication |
| `/` | division (whole numbers only) |
| `%` | remainder |

Save a result in a variable:

```bash
sum=`expr 10 + 20`
echo "sum is = $sum"
```

## 9. Quotes

| Quote | Variables | Commands | Use |
|---|---|---|---|
| `' '` single | no | no | print text exactly as written |
| `" "` double | yes | yes (with `$( )`) | print text with values |
| `` ` ` `` back | | yes | run a command and capture output |

```bash
name=amir
echo 'hello $name'          # hello $name
echo "hello $name"          # hello amir
echo `date`
echo "today is $(date +%a)"
echo "my system name is $HOSTNAME"
```

`$(command)` is the modern replacement for back quotes and is preferred.

## 10. sum.sh and read

`sum.sh`:

```bash
#!/bin/bash
a=10
echo $a
num=$(date +%a)
echo $num
x=20
y=40
sum=`expr $x + $y`
echo "sum of x and y is = $sum"
echo `hostname`
```

`read.sh` (input from the user):

```bash
#!/bin/bash
echo "Enter first value"
read x
echo "Enter second value"
read y
sum=`expr $x + $y`
echo "sum of x and y is = $sum"
```

Shorter version with `read -p`:

```bash
#!/bin/bash
read -p "Enter first value: " x
read -p "Enter second value: " y
echo "sum is = $(expr $x + $y)"
```

## 11. name.sh and system report

`name.sh`:

```bash
#!/bin/bash
echo "what is your name"
read name
echo "your name is $name"
```

`report.sh` saves output to a file:

```bash
#!/bin/bash
report=/tmp/report.txt
echo "System Report" > $report
date >> $report
uptime >> $report
df -h >> $report
free -hm >> $report
```

| Symbol | Meaning |
|---|---|
| `>` | write to a file, **replaces** old content |
| `>>` | add to the end of a file, **keeps** old content |

| Command | Shows |
|---|---|
| `df -h` | disk space in readable units |
| `free -hm` | memory (RAM) usage |

View the result: `cat /tmp/report.txt`

## 12. Common mistakes

- Using curly quotes (“ ”) copied from documents. Always type straight quotes (`" "`).
- Spaces around `=` in variables.
- Missing spaces around operators in `expr`.
- Writing `expr $x * $y` instead of `expr $x \* $y`.
- Using `env` to look for a local variable (use `set`, or `export` it).
- Running `./file.sh` without `chmod +x`.
- Using `>` on every line of a report, which erases the previous lines.

## Practice exercises

1. `info.sh`: store your name and city in variables and print one sentence.
2. `calc.sh`: with `a=15`, `b=4`, print sum, difference, product, division and remainder.
3. `quotes.sh`: print one line with single quotes, one with double quotes and a variable, and one with `$(date +%a)`.
4. Extend `report.sh` with `whoami` and `hostname`.

---

Next: Lecture 2, operators.
