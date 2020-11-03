# shell-scripts
Collection of useful shell scripts.  
All the scripts within this document is tested to be compatible with `bash` that comes w/ FreeBSD.  
YMMV on other OS. 

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
bc -l

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

echo "scale=2; $PHYSMEM/1024/1024/1024" | bc -l

sysctl -n kern.hostname

ps -o user,pid,ppid,stat,%cpu,%mem -p

sockstat -4 -l -q

last

sort | uniq -c 

trap ctrl_c SIGINT
ctrl_c(){
    echo "Ctrl+C pressed.">&1
    return 2
}

#/bin/bash