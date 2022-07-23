# Shell Scripting
A collection of common scripting blocks

## Functions
### Basic definition
```
function example_function() {
    echo "This is an example."
}

example_function() {
    echo "This is an example."
}
```

### Convert Seconds to Friendly Time Format

```
$ DURATION=499

$ function displaytime {
  local T=$1
  local D=$((T/60/60/24))
  local H=$((T/60/60%24))
  local M=$((T/60%60))
  local S=$((T%60))
  (( $D > 0 )) && printf '%d days ' $D
  (( $H > 0 )) && printf '%d hours ' $H
  (( $M > 0 )) && printf '%d minutes ' $M
  (( $D > 0 || $H > 0 || $M > 0 )) && printf 'and '
  printf '%d seconds\n' $S
}

$ displaytime $DURATION
8 minutes and 19 seconds
```


## Return Codes
### Read last command's return code
```
$ echo $?
```

Check if value is populated
```
$ [ $FOO ]
$ echo $?
1 

$ FOO="BAR"
$ [ $FOO ]
$ echo $?
0 
```


## Format Date
### Basic / ISO 8601
Is there another time format?
```
$ TIME=$(date +"%Y-%m-%dT%H:%M:%S%z")
$ echo $TIME
2022-07-15T21:57:22-0700
```


## Execute bash function on remote host
We can use typset to set a function as a variable and pass it to a host with ssh

```
function echo_hostname() {
    echo $(hostname)
}

$ echo_hostname 
host1

$ ssh user@host2 "$(typeset -f echo_hostname); echo_hostname"
host2
```