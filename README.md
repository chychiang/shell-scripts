# shell-scripts
Collection of useful shell scripts.  
All the scripts within this document is tested to be compatible with `bash` that comes w/ FreeBSD.  
YMMV on other OS. 

## pure-ftpd related
### Managing Virtual Users
The server never reads ```/etc/pureftpd.passw``` ddirectly. Instead, it reads ```/etc/pureftpd.pdb``` (or whatever file name you gave after -lpuredb:...) .

This file is a copy of ```/etc/pureftpd.passwd```, but in a binary format,
optimized for fast lookups.

After having made a manual change to /etc/pureftpd.passwd, you must rebuild
```/etc/pureftpd.pdb``` with ```pure-pw mkdb```

If you add/delete/modify user accounts with ```pure-pw useradd/usermod/userdel/passwd```, don't forget the ```-m``` option to automatically rebuild ```/etc/pureftpd.pdb``` and not only update ```/etc/pureftpd.passwd```


## dialog
### User interaction of the dialog interface
Specifies what happens when the user press on the navigation buttons on the bottom of the dialog.
```bash
OK=0
CANCEL=1
ESC=255

if [ $result -eq $OK ]; then
    # pass in arg and execute function 
    func $CHOICE
elif [ $result -eq $CANCEL ] || [ $result -eq $ESC ]; then
    # go back to parent menu or specified function
    return
fi
```

## awk 
### Print only column 2
```bash
awk '{print$2}'
```
### Print starting line 5
```bash
awk 'NR > 4 {print$2}'
```
### Process the filtered data w/ piped processes
Note: always close the pipe with `close()` before printing other content.
```bash
awk '{
    command="sort -k4 -n -r | head -5"; 
    print | command;
    close(command);
}'
```
### Store a particular column of data in an `awk` array to use it later
```bash
awk '
BEGIN {
    i=0;
}
NR > 1 {
    array[i++];
}
END {
    for(i in array) {
        print i;
        print array[i];
        # process the data
    }
}
'
```

## [sed](https://www.freebsd.org/cgi/man.cgi?query=sed&manpath=FreeBSD+12.2-RELEASE+and+Ports)
### **s**tream **ed**itor
```bash
sed 's/Mounted on/Mounted_on/'  # replace "Mounted on" w/ "Mounted_on"
```

## df
### **d**isplay **f**ree disk space 
```bash
df -h -t nfs,zfs
# -h            human readable - Kibibyte, Mebibyte,... (1024)
# -H            human readable - Kilobyte, Megabyte,... (1000)
# -t nfs,zfs    only display nfs and zfs typed filesystem
```
## sort
```bash
sort -k4 -n -r 
# -k4   sort by column 4
# -n    sort by numeric values
# -r    reversed order (default is descending)

sort -u
# -u    sort out unique keys only, often use in conjunction w/ wc
sort -u | wc -l 
# count unique values in each line
```

## head
```bash
head -5 # display the first 5 line of a file / expression
```

## cut
Useful utility for cutting row / col
```bash
cut -w -f1,2,3,5
# -w            delim by whitespace
# -d " "        set the delimiter to be " "
# -f1,2,3,5     preserve column 1,2,3,5
```

## tr
### **tr**aslate character
```bash
tr -s " "   # normalize any whitespace to one single space
```

## [sysctl](https://www.freebsd.org/cgi/man.cgi?query=sysctl&sektion=8&manpath=FreeBSD+12.2-RELEASE+and+Ports)
Retrives the kernel state, often used to get useful info about the system.
```bash
sysctl kern.hostname  # get hostname of the kernel
sysctl hw.physmem   # get amount of phsical memory of the machine
```

## [ps](https://www.freebsd.org/cgi/man.cgi?query=ps&sektion=1&manpath=FreeBSD+12.2-RELEASE+and+Ports)
```bash
ps -o user,pid,ppid,stat,%cpu,%mem -p <PID>
# -o    specify what keywords (fields) to display
# -p    specify the PID to display info
```

## Misc
### chage the text color 
Useful for highlighting output. Always reset the color after stdout.
```bash
\033[31m    # red
\033[32m    # green
\033[33m    # yellow
\033[0m     # reset text colors
```

### foil / expand an array 
Can be used inside awk
```bash
${arr[@]} 
```

### transpose whitespace delimitered table
```bash
rs -c' ' -T
# -c' '     set the input delimiter to be ' '
# -T        transpose
```

### get current date
```bash
date
```

### File descriptors
```bash
&0  # stdin
&1  # stdout
&2  # stderr

# therefore, this redirects stdout to stderr
1>&2
```

### Calculate float point numbers
Bash does not support calculation of float point numbers.
For instance, 5/4 will result in 0.  
[`bc`](https://www.freebsd.org/cgi/man.cgi?query=bc&sektion=1) is an useful tool when dealing with arithmetics of float point numbers.
```bash
# converting bytes into gigabytes
echo "scale=2; $PHYSMEM/1024/1024/1024" | bc -l

# scale=2;  tells bc to only display 2 digits of decimals
# bc -l     allows specification of precision
```

### Count how many times values occured
```bash
sort | uniq -c 
# always sort before uniq, as uniq only counts neighboring repeating values
# -c    count occurance of unique values
```

## [sockstat](https://www.freebsd.org/cgi/man.cgi?query=sockstat&sektion=1&manpath=FreeBSD+12.2-RELEASE+and+Ports)
Lists open sockets
```bash
sockstat -4 -l -q
# -4    show only IPv4 sockets
# -l    show listening sockets
# -q    don't print header line, useful when piping
```

## [last](https://www.freebsd.org/cgi/man.cgi?query=last&sektion=1&manpath=FreeBSD+12.2-RELEASE+and+Ports)
Indicate last logins of users and ttys
```bash
last -d # to specify a snapshot date / time
last | awk -v month="$THIS_MONTH" '$0 ~ month {print$1}' # print the data of current month
```

## Trapping signals (Ctrl+C)
```bash
trap ctrl_c SIGINT  # SIGINT = Ctrl+C in FreeBSD
ctrl_c(){
    echo "Ctrl+C pressed.">&1   # echo to stdout
    return 2    # stop the function / program w/ an error code
}
```

## Shebang
Put this at the beginning of a `.sh` file to specify what shell the script should be executed with.
```bash
#!/bin/bash     # use bash to execute the following
```