# Unix

## Input/Output Redirection

* `>`: put the standard output into the file
* `>>`: append the standard output to the file
* `<`: read the standard input from the file

## Processes

* Find the limits of a process
```
cat /proc/<PID>/limits
```
* Find a PID by name
```
ps aux | grep name
# Or
pgrep name
```
* Find all processes for the current group
```
ps -ag
```
* Run a process that is not attached to the terminal
```
nohup <command> &
```

## Filesystem

* Display file content in octal, decimal or hexadecimal format
```
od -cb <filename>
```

### Permissions

| mode | octal | file    | directory   |
| ---- | ----- | ------- | ----------- |
| r    | 1     | read    | read        |
| w    | 2     | write   | edit/delete |
| x    | 4     | execute | search      |

* A file has a set of permission associated with it
```
$ ls -l /etc/passwd
-rw-r--r-- root root
# group permission (-rw-) | user permission (r--) | other permission (r--) | owener | group
```
* A directory is prefixed with `d`
```
$ ls -ld /
drwxr-xr-x 26 root root
```
* Allow all users to read and execute the file
```
chmod a+rx file
```
* Allow the user who owns the file to execute it
```
chmod u+x file
```
* Turn off write permission for the group
```
chmod g-w file
```
* If a directory is writable, anyone can remove/edit files regardless of the persmission on the files themselves

### Inodes

Administrative information is stored in the inode along with its location on disc, how long it is.
Symbolic links point to the underlying inode of the file.
```
$ ln old_file new_file
$ ls -li
7105506 -rw-rw-r-- 2 chouffe chouffe 59 Jun 15 13:29 new_file
7105506 -rw-rw-r-- 2 chouffe chouffe 59 Jun 15 13:29 old_file
```

### /dev/null

* Discard output
```
command > /dev/null
```

## I/O redirection

* `> file`: writes standard output to `file`
* `2>&1`: writes standard error (2) to standard output (1)
* `> file 2>&1`: writes standard output and error to `file`
* `&> out`: writes standard error and standard output to `out`

* Shell jargon example
```
grep "$*" <<EOF
command1
command2
command3
EOF
```

### Built in Variables

| Variable | Description                                     |
| -------- | ----------------------------------------------- |
| `$#`     | Number of arguments                             |
| `$*`     | All arguments to shell                          |
| `$@`     | All arguments to shell                          |
| `$-`     | Options supplied to shell                       |
| `$?`     | Return value of the last command executed       |
| `$$`     | Process ID of the shell                         |
| `$!`     | Process ID of the last command started with `&` |

### Signals

| Signal Name | Signal Number | Description                                                                                         |
| ----------- | ------------- | --------------------------------------------------------------------------------------------------- |
| SIGHUP      | 1             | Hang up detected on controlling terminal or death of controlling process                            |
| SIGINT      | 2             | Issued if the user sends an interrupt signal (Ctrl + C)                                             |
| SIGQUIT     | 3             | Issued if the user sends a quit signal (Ctrl + D)                                                   |
| SIGFPE      | 8             | Issued if an illegal mathematical operation is attempted                                            |
| SIGKILL     | 9             | If a process gets this signal it must quit immediately and will not perform any clean-up operations |
| SIGALRM     | 14            | Alarm clock signal (used for timers)                                                                |
| SIGTERM     | 15            | Software termination signal (sent by kill by default)                                               |

```
$ kill -l          # HUP INT QUIT ILL TRAP ABRT BUS FPE KILL USR1 SEGV USR2 PIPE ALRM TERM STKFLT CHLD CONT STOP TSTP TTIN TTOU URG XCPU XFSZ VTALRM PROF WINCH POLL PWR SYS
$ kill -l SIGHUP   # 1
$ kill -l 1        # SIGHUP
```

* Send signals to a process
```
# kill -signal pid
$ kill -9 12345
$ kill -kill 12345
```

* Set a trap which, on shell error or shell exit, deletes a temporary file `xyz$$`.
```
trap 'rm -f /tmp/xyz$$; exit' ERR EXIT
```
* Set a trap which, on shell exit, kills the last command executed with `&`.
```
trap 'kill $!' EXIT
```

* trap full example
```
#!/bin/sh

trap cleanup 1 2 3 6

cleanup()
{
  echo "Caught Signal ... cleaning up."
  rm -rf /tmp/temp_*.$$
  echo "Done cleanup ... quitting."
  exit 1
}

### main script
for i in *
do
  sed s/FOO/BAR/g $i > /tmp/temp_${i}.$$ && mv /tmp/temp_${i}.$$ $i
done
```

# From The Unix Programming Environment book

## Common File System Commands

| Command                | Action                                                |
|------------------------|-------------------------------------------------------|
| ls                     | list names of all files in current directory          |
| ls filenames           | list only the named files                             |
| ls -t                  | list in time order, most recent first.                |
| ls -1                  | list long: more information; also ls-lt               |
| ls -u                  | list by time last used; also ls -lu, ls -lut          |
| ls -r                  | list in reverse order; also -rt, -rlt, etc.           |
| ed filename            | edit named file                                       |
| cp filel file?         | copy filel to file2, overwrite old file2 if it exists |
| mv filel file2         | move fill to file2, overwrite old file2 if it exists  |
| rm filenames           | remove named files, irrevocably                       |
| cat filenames          | print contents of named files                         |
| pr filenames           | print contents with header, 66 lines per page         |
| pr -n filenames        | print in n columns                                    |
| pr -m filenames        | print named files side by side (multiple columns)     |
| wc filenames           | count lines, words and characters for each file       |
| wc -1 filenames        | count lines for each file                             |
| grep pattern filenames | print lines matching pattern                          |
| grep -v pattern files  | print lines not matching pattern                      |
| sort filenames         | sort files alphabetically by line                     |
| tail filename          | print last 10 lines of file                           |
| tail -n filename       | print last n lines of file                            |
| tail +n filename       | start printing file at line n                         |
| cmp file1 file2        | print location of first difference                    |
| diff file1 file2       | print all differences between files                   |
