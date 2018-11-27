# File Navigation and Manipulation
#### `ls` - list directory contents
* `-l` long listing format
* `-a` include hidden (. prefix)
* `-d` list directories (not their contents)
* `--sort=time/size
#### Globbing - `*` wild  card
#### `pwd` - print working directory
#### `cd` - change directory
* `.` - current working directory
* `..` - up a level
* `~` - home
#### `cp <src> <dst>` -- copy files
* `-r` copy a directory recursively
#### `mv <src> <dst>` -- move files
#### `mkdir <dir>` -- create a new directory
#### `rm` - delete files
* `-r` - delete recursively (used for deleting a directory)
#### `chmod` -- change file permissions
#### `source <file>` -- run commands in file
#### `which <command>` -- locate command binary
#### `man <command>` -- show manual page for command


# Bash History and Shortcuts
#### up arrow -- previous command
#### Ctrl-R -- search history
#### Ctrl-D -- EOF/logout/exit
#### Ctrl-A -- goto start of line
#### Ctrl-E -- goto end of line
#### TAB -- autocomplete file or command
#### `alias <alias>=<cmd>` -- create a shorter name for a command
* Ex: `alias qs='qstat -t -n -1'`


# I/O Redirection
#### `>` -- send standard output to file
* Ex: `cat *.txt > all.txt`
#### `>>` -- append standard output to file
#### `<` -- get standard in from file
#### `|` -- pipe output to input
* Ex: `ls | sort`
#### `2>` -- send standard error to file
* Ex: `cmd > output 2> errors`
#### `&>` -- send standard output AND error to file


# Loops
    for i in [list]
    do
    <cmd> .. $i ...
    done

[list] Examples:

    {1..24}
    *.sdf
    `cat files`

# `awk` -- pattern scanning and processing language
#### `-F<x>` - make `x` the field delimiter (default is whitespace)
#### `NF` - number of fields (columns) on line
#### `NR` - current record number
#### `$0` - full line
#### `$<N>` - Nth field of line
* Example 1: Print first three fields of line  `awk '{print $1,$2,$3}' file`
* Example 2: Print file without header  `awk 'NR > 1 {print $0}' file`
* Example 3: Print first field of lines where second field is greater than zero `awk '$2 > 0 {print $1}' file

# Text Manipulation
#### `cat <file>` -- print out file contents
#### `head <file>` -- print first 10 lines of file
* `-n <N>` show first N lines
#### `tail <file>` -- print last 10 lines of file
* `-n <N>` show last N lines
#### `wc <file>` -- print line, word, and character counts in file
#### `sort <file>` -- sort contents of file, line by line
* `-n` - numeric sort
* `-kN,M` - sort based on columns N-M
* `-r` - reverse sort
* `-R` - random sort
* `-u` - uniquify 
* `-o <outfile>` - write output to outfile instead of standard out
#### `uniq` -- count/omit repeated lines
* `-c` - prefix lines by number of occurences
#### `cut` -- cut out columns from a file
#### `grep <pattern> <file(s)>` -- search file contents
* `-i` - ignore capitalization
* `-r` - recursive search
* `-I` - skip over binary files
* `-s` - suppress error messages
* `-n` - show line numbers
* `-A<N>` - show N lines after match
* `-B<N>` - show N lines before match
#### `sed` -- filter and transform text
* `-i` - edit files in place
* Example 1: Change first HIE to HIS `sed 's/HIE/HIS/' file.pdb > new.pdb`
* Example 2: Change ALL HIE to HIS **inplace** `sed 's/HIE/HIS/g' file.pdb`


# Environment Variables
#### `echo <string>` -- print string to terminal
#### `env` -- print all set environment variables
#### `.bashrc` -- hidden file in HOME that initializes BASH (editing this file will make changes "stick")
#### `export VAR=def` -- define VAR to def
#### `$` -- show variable contents
* Example: `echo $PATH` and `echo ${PATH}`
#### PATH -- where shell searches for executables
#### LD_LIBRARY_PATH -- library search path
#### PYTHONPATH -- where python searches for modules