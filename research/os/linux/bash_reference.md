### Your First Script

```bash
#!/bin/bash
# This is a comment
# The shebang (#!) tells the OS which interpreter to use

echo "Hello, World!"
```

Make it executable and run it:
```bash
chmod +x script.sh
./script.sh
# Or explicitly:
bash script.sh
```

### Variables

```bash
#!/bin/bash

# Assign (no spaces around =)
name="Alice"
age=30
pi=3.14

# Use (prefix with $)
echo "Name: $name"
echo "Age: $age"

# Curly braces (safer, clearer)
echo "Name: ${name}"
echo "${name}s"         # "Alices" — braces allow suffix

# Read-only
readonly CONSTANT="value"

# Unset
unset name

# Command substitution (capture output)
current_dir=$(pwd)
date_str=$(date +%Y-%m-%d)
files=$(ls /etc)

# Arithmetic
count=$((5 + 3))
echo $((10 * 3))
echo $((100 / 4))
echo $((10 % 3))        # Modulo = 1

# let command
let result=5+3
```

### Special Variables

```bash
$0          # Script name
$1, $2...   # Positional arguments (arg 1, arg 2...)
$#          # Number of arguments
$@          # All arguments (each quoted separately)
$*          # All arguments (as single string)
$?          # Exit status of last command (0=success, non-zero=failure)
$$          # PID of current script
$!          # PID of last background command
$LINENO     # Current line number
$RANDOM     # Random integer 0-32767
$HOME       # Home directory
$USER       # Current username
$HOSTNAME   # Machine hostname
$SHELL      # Current shell path
$PWD        # Current directory
$OLDPWD     # Previous directory
$PATH       # Executable search path
$IFS        # Internal field separator (default: space/tab/newline)
```

### User Input

```bash
read varname                        # Read from stdin
read -p "Enter name: " name         # With prompt
read -s -p "Password: " pass        # Silent (no echo)
read -t 10 -p "10 second timeout: " input  # Timeout
read -r line                        # Raw (don't interpret backslashes)
read -a array                       # Read into array
```

### String Operations

```bash
str="Hello, World"

# Length
echo ${#str}                # 13

# Substring: ${var:start:length}
echo ${str:0:5}             # Hello
echo ${str:7}               # World (to end)
echo ${str: -5}             # World (from end)

# Replace: ${var/pattern/replacement}
echo ${str/World/Bash}      # Hello, Bash (first occurrence)
echo ${str//l/L}            # HeLLo, WorLd (all occurrences)

# Remove prefix/suffix
file="archive.tar.gz"
echo ${file%.gz}            # archive.tar (remove shortest .gz)
echo ${file%.*}             # archive.tar (remove shortest .ext)
echo ${file%%.*}            # archive (remove longest .ext)
echo ${file#*.}             # tar.gz (remove shortest prefix to .)
echo ${file##*.}            # gz (remove longest prefix to .)

# Case conversion
echo ${str,,}               # hello, world (lowercase)
echo ${str^^}               # HELLO, WORLD (uppercase)

# Default values
echo ${undefined:-"default"}    # "default" if undefined
echo ${var:="default"}          # Set and use default if undefined
```

### Conditionals — if/elif/else

```bash
#!/bin/bash

# Basic if
if [ condition ]; then
    commands
fi

# if/else
if [ condition ]; then
    commands
else
    commands
fi

# if/elif/else
if [ condition1 ]; then
    commands
elif [ condition2 ]; then
    commands
else
    commands
fi
```

### Test Conditions

**String tests:**
```bash
[ "$a" = "$b" ]         # Equal (use = not == in [ ])
[ "$a" != "$b" ]        # Not equal
[ -z "$a" ]             # Empty string
[ -n "$a" ]             # Non-empty string
[[ "$a" == "h*" ]]      # Pattern match (only in [[ ]])
[[ "$a" =~ ^[0-9]+$ ]]  # Regex match (only in [[ ]])
```

**Numeric tests:**
```bash
[ $a -eq $b ]           # Equal
[ $a -ne $b ]           # Not equal
[ $a -lt $b ]           # Less than
[ $a -le $b ]           # Less than or equal
[ $a -gt $b ]           # Greater than
[ $a -ge $b ]           # Greater than or equal
```

**File tests:**
```bash
[ -e file ]             # Exists
[ -f file ]             # Is a regular file
[ -d dir ]              # Is a directory
[ -L link ]             # Is a symbolic link
[ -r file ]             # Readable
[ -w file ]             # Writable
[ -x file ]             # Executable
[ -s file ]             # Non-empty (size > 0)
[ -O file ]             # Owned by current user
[ -G file ]             # Owned by current group
[ file1 -nt file2 ]     # file1 newer than file2
[ file1 -ot file2 ]     # file1 older than file2
```

**Combining conditions:**
```bash
[ cond1 ] && [ cond2 ]      # AND
[ cond1 ] || [ cond2 ]      # OR
! [ cond ]                   # NOT

# [[ ]] supports && and || directly
[[ cond1 && cond2 ]]
[[ cond1 || cond2 ]]
```

### Prefer `[[ ]]` Over `[ ]`

`[[ ]]` is a bash keyword, more powerful and safer:
```bash
# [ ] fails if variable is empty (becomes [ = "yes" ])
# [[ ]] handles it safely
[[ "$var" == "yes" ]]       # Safe
[[ "$var" =~ [0-9]+ ]]     # Regex — only works in [[
[[ -f "$file" && -r "$file" ]]   # AND inside [[
```

### case Statement

```bash
case "$variable" in
    "value1")
        commands
        ;;
    "value2"|"value3")      # Multiple values
        commands
        ;;
    *.txt)                  # Glob pattern
        commands
        ;;
    *)                      # Default
        commands
        ;;
esac
```

### Loops

**for loop:**
```bash
# List
for item in apple banana cherry; do
    echo "$item"
done

# C-style
for ((i=0; i<10; i++)); do
    echo "$i"
done

# Range
for i in {1..10}; do
    echo "$i"
done

for i in {0..100..10}; do    # With step
    echo "$i"
done

# Files
for file in /etc/*.conf; do
    echo "Processing: $file"
done

# Command output
for user in $(cut -d: -f1 /etc/passwd); do
    echo "User: $user"
done
```

**while loop:**
```bash
# Condition
count=0
while [ $count -lt 10 ]; do
    echo "$count"
    ((count++))
done

# Read file line by line
while IFS= read -r line; do
    echo "$line"
done < file.txt

# Infinite loop
while true; do
    echo "Running..."
    sleep 5
done

# Read command output
command | while IFS= read -r line; do
    process "$line"
done
```

**until loop:**
```bash
# Runs until condition is true (opposite of while)
until [ $count -ge 10 ]; do
    echo "$count"
    ((count++))
done
```

**Loop control:**
```bash
break           # Exit loop
continue        # Skip to next iteration
break 2         # Break out of 2 nested loops
```

---

## Chapter 9: Bash Scripting — Intermediate

### Functions

```bash
#!/bin/bash

# Define function
greet() {
    local name="$1"     # Local variable — doesn't leak outside
    echo "Hello, $name!"
    return 0            # Return exit status (0-255)
}

# Call function
greet "Alice"
greet "Bob"

# Capture return value
result=$(greet "Charlie")
echo "$result"

# Function with error checking
check_root() {
    if [ "$(id -u)" -ne 0 ]; then
        echo "Error: must run as root" >&2
        return 1
    fi
    return 0
}

check_root || exit 1    # Exit script if not root
```

**Important:** Use `local` for all function variables. Without it, they pollute the global scope.

### Arrays

```bash
# Indexed arrays
fruits=("apple" "banana" "cherry")
fruits[3]="date"

echo "${fruits[0]}"         # First element
echo "${fruits[@]}"         # All elements
echo "${#fruits[@]}"        # Array length
echo "${!fruits[@]}"        # All indices

# Append
fruits+=("elderberry")

# Slice
echo "${fruits[@]:1:2}"     # Elements 1 and 2

# Iterate
for fruit in "${fruits[@]}"; do
    echo "$fruit"
done

# Delete element
unset fruits[2]

# Associative arrays (bash 4+)
declare -A person
person["name"]="Alice"
person["age"]="30"
person["city"]="NYC"

echo "${person["name"]}"
echo "${!person[@]}"        # All keys
echo "${person[@]}"         # All values

for key in "${!person[@]}"; do
    echo "$key: ${person[$key]}"
done
```

### Error Handling

```bash
#!/bin/bash
set -e              # Exit on any error
set -u              # Exit on undefined variable
set -o pipefail     # Catch errors in pipes
set -x              # Debug mode: print each command before executing

# All together (common practice):
set -euo pipefail

# Trap — run code when script exits or errors
cleanup() {
    echo "Cleaning up..."
    rm -f /tmp/tempfile
}
trap cleanup EXIT           # Run on any exit
trap 'echo "Error on line $LINENO"' ERR  # Run on error

# Check exit status
if ! command; then
    echo "Command failed" >&2
    exit 1
fi

# Or use || 
command || { echo "Failed"; exit 1; }

# Stderr redirection
echo "Error message" >&2    # Write to stderr, not stdout
```

### String Manipulation Advanced

```bash
# Split string into array
IFS=',' read -r -a array <<< "a,b,c,d"
echo "${array[0]}"   # a
echo "${array[2]}"   # c

# Join array
arr=("a" "b" "c")
joined=$(IFS=','; echo "${arr[*]}")
echo "$joined"       # a,b,c

# Trim whitespace
trim() {
    local str="$1"
    str="${str#"${str%%[![:space:]]*}"}"   # ltrim
    str="${str%"${str##*[![:space:]]}"}"   # rtrim
    echo "$str"
}

# Check if string contains substring
if [[ "$haystack" == *"$needle"* ]]; then
    echo "Found"
fi

# Pad string
printf "%-20s %s\n" "left-padded" "value"
printf "%20s %s\n"  "right-padded" "value"
```

### Process Substitution

```bash
# Feed command output as if it were a file
diff <(sort file1) <(sort file2)

# Compare two command outputs
comm <(sort list1) <(sort list2)

# While loop that doesn't fork a subshell
while IFS= read -r line; do
    echo "$line"
done < <(find /etc -name "*.conf")
```

### Here Documents and Here Strings

```bash
# Here document
cat <<EOF
Line 1
Line 2
Variable: $USER
EOF

# No variable expansion
cat <<'EOF'
Literal $variable — no expansion
EOF

# Indented
cat <<-EOF
    This is indented
    Tab is stripped by -
EOF

# Here string
grep "pattern" <<< "string to search"
read var <<< "value"
```

### Parsing Command-Line Arguments

```bash
#!/bin/bash

usage() {
    echo "Usage: $0 [-v] [-f file] [-n count] target"
    exit 1
}

verbose=false
file=""
count=10

# getopts — simple option parsing
while getopts "vf:n:h" opt; do
    case "$opt" in
        v) verbose=true ;;
        f) file="$OPTARG" ;;
        n) count="$OPTARG" ;;
        h) usage ;;
        ?) usage ;;
    esac
done

# Shift past the options
shift $((OPTIND - 1))

# Remaining args are positional
target="$1"

echo "verbose=$verbose file=$file count=$count target=$target"
```

### Debugging Scripts

```bash
bash -x script.sh           # Trace execution (print each command)
bash -n script.sh           # Check syntax without running
bash -v script.sh           # Print lines as read

# In-script
set -x                      # Enable trace
set +x                      # Disable trace

# Debug specific section
set -x
some_command
other_command
set +x

# Print to stderr for debugging
echo "DEBUG: var=$var" >&2
```

---

## Chapter 10: Bash Scripting — Advanced

### Subshells and Grouping

```bash
# Subshell — runs in separate process, changes don't affect parent
(cd /tmp && ls)             # cd only affects the subshell
echo $?                     # Exit status of subshell

# Group — runs in current shell
{ cd /tmp; ls; }
# Note: space after { and ; before } are required

# Differences:
(var=subshell)
echo "$var"     # Empty — subshell changes don't propagate

{ var=group; }
echo "$var"     # "group" — group affects current shell
```

### Named Pipes (FIFOs)

```bash
mkfifo /tmp/mypipe              # Create named pipe
command1 > /tmp/mypipe &        # Write to pipe
command2 < /tmp/mypipe          # Read from pipe

# Example: log to file and screen simultaneously
mkfifo /tmp/log_pipe
tee /tmp/logfile < /tmp/log_pipe &
./myapp > /tmp/log_pipe
```

### Coprocesses

```bash
# Run command in background with two-way communication
coproc myproc { command; }
echo "input" >&${myproc[1]}     # Write to coprocess
read response <&${myproc[0]}    # Read from coprocess
```

### File Locking

```bash
# Prevent multiple instances of a script
LOCKFILE="/tmp/myscript.lock"

if ! mkdir "$LOCKFILE" 2>/dev/null; then
    echo "Script already running" >&2
    exit 1
fi
trap "rmdir '$LOCKFILE'" EXIT

# Using flock
exec 9>"$LOCKFILE"
flock -n 9 || { echo "Already running"; exit 1; }
```

### Advanced Text Processing

```bash
# Multi-line sed
sed ':a;N;$!ba;s/\n/ /g' file      # Join all lines

# Print between two patterns
awk '/START/,/END/' file

# Sum a column
awk '{sum+=$1} END{print sum}' file

# Count unique values in column 2
awk '{count[$2]++} END{for(k in count) print count[k], k}' file | sort -rn

# Join two files on a common field
join -t: -1 1 -2 1 <(sort /etc/passwd) <(sort /etc/shadow)
```

### Parallel Execution

```bash
# Simple parallel
command1 &
command2 &
command3 &
wait                            # Wait for all

# GNU parallel (if installed)
parallel -j4 process_file {} ::: *.log

# xargs parallel
find . -name "*.log" | xargs -P4 -I{} gzip {}

# Manual pool
MAX_JOBS=4
running=0
for file in *.log; do
    process "$file" &
    ((running++))
    if [ $running -ge $MAX_JOBS ]; then
        wait -n 2>/dev/null || wait
        ((running--))
    fi
done
wait
```

### Scripting Patterns for Security

```bash
# Validate IP address
validate_ip() {
    local ip="$1"
    if [[ "$ip" =~ ^([0-9]{1,3}\.){3}[0-9]{1,3}$ ]]; then
        IFS='.' read -ra octets <<< "$ip"
        for octet in "${octets[@]}"; do
            if [ "$octet" -gt 255 ]; then
                return 1
            fi
        done
        return 0
    fi
    return 1
}

# Safe temp file creation
tmpfile=$(mktemp /tmp/script.XXXXXX)
trap "rm -f '$tmpfile'" EXIT

# Sanitize user input
sanitize() {
    echo "$1" | tr -dc '[:alnum:]._-'
}

# Check command exists
require_command() {
    command -v "$1" >/dev/null 2>&1 || {
        echo "Required command not found: $1" >&2
        exit 1
    }
}
require_command curl
require_command jq
```

---

## Chapter 11: Security-Focused Bash

### System Enumeration Scripts (for SOC/Pen Testing)

```bash
#!/bin/bash
# Basic Linux enumeration script

echo "=== SYSTEM INFO ==="
uname -a
cat /etc/os-release

echo "=== CURRENT USER ==="
id
whoami
sudo -l 2>/dev/null

echo "=== NETWORK INTERFACES ==="
ip addr

echo "=== LISTENING PORTS ==="
ss -tulnp 2>/dev/null || netstat -tulnp 2>/dev/null

echo "=== ESTABLISHED CONNECTIONS ==="
ss -tnp state established

echo "=== PROCESSES ==="
ps aux --forest

echo "=== CRON JOBS ==="
crontab -l 2>/dev/null
ls -la /etc/cron* /etc/cron.d/ 2>/dev/null

echo "=== SUID BINARIES ==="
find / -perm -4000 -type f 2>/dev/null

echo "=== WORLD-WRITABLE DIRS ==="
find / -perm -2 -type d 2>/dev/null | grep -v proc

echo "=== RECENT MODIFIED FILES ==="
find / -mtime -1 -type f 2>/dev/null | grep -v proc | head -50

echo "=== USERS WITH SHELL ACCESS ==="
grep -v nologin /etc/passwd | grep -v false

echo "=== SUDO USERS ==="
cat /etc/sudoers 2>/dev/null
getent group sudo wheel 2>/dev/null
```

### Log Analysis Script

```bash
#!/bin/bash
# Analyze auth logs for suspicious activity

LOG="/var/log/auth.log"
[ -f /var/log/secure ] && LOG="/var/log/secure"  # RedHat/CentOS

echo "=== Failed Login Attempts by IP ==="
grep "Failed password" "$LOG" | awk '{print $11}' | sort | uniq -c | sort -rn | head -20

echo ""
echo "=== Failed Logins by Username ==="
grep "Failed password" "$LOG" | awk '{print $9}' | sort | uniq -c | sort -rn | head -20

echo ""
echo "=== Successful Logins ==="
grep "Accepted" "$LOG" | awk '{print $9, $11}' | sort | uniq -c | sort -rn

echo ""
echo "=== Root Login Attempts ==="
grep "root" "$LOG" | grep -E "Failed|Accepted"

echo ""
echo "=== sudo Usage ==="
grep "sudo" "$LOG" | tail -20
```

### Port Scanner (Educational)

```bash
#!/bin/bash
# Simple port scanner using /dev/tcp

scan_port() {
    local host="$1"
    local port="$2"
    timeout 1 bash -c "echo >/dev/tcp/$host/$port" 2>/dev/null && echo "OPEN: $port"
}

if [ $# -lt 2 ]; then
    echo "Usage: $0 host start_port [end_port]"
    exit 1
fi

HOST="$1"
START="$2"
END="${3:-$START}"

echo "Scanning $HOST ports $START-$END..."
for port in $(seq "$START" "$END"); do
    scan_port "$HOST" "$port" &
done
wait
```

### Network Monitoring Script

```bash
#!/bin/bash
# Monitor for new connections

monitor_connections() {
    local interval="${1:-5}"
    local prev_file=$(mktemp)
    local curr_file=$(mktemp)
    trap "rm -f $prev_file $curr_file" EXIT

    ss -tn state established | awk 'NR>1 {print $4, $5}' | sort > "$prev_file"

    while true; do
        sleep "$interval"
        ss -tn state established | awk 'NR>1 {print $4, $5}' | sort > "$curr_file"
        
        new=$(comm -13 "$prev_file" "$curr_file")
        closed=$(comm -23 "$prev_file" "$curr_file")
        
        [ -n "$new" ] && echo "[$(date)] NEW: $new"
        [ -n "$closed" ] && echo "[$(date)] CLOSED: $closed"
        
        cp "$curr_file" "$prev_file"
    done
}

monitor_connections 5
```

### Exercises — Bash

**Exercise 1 (Beginner):** Write a script that takes a directory as an argument, lists all `.log` files, and for each one prints its name and line count.

**Exercise 2 (Beginner):** Write a script that checks if a website is up (using curl). Accept the URL as an argument. Exit with 0 if up, 1 if down.

**Exercise 3 (Intermediate):** Write a script that monitors `/var/log/auth.log` in real-time (using `tail -f`) and alerts (prints to screen) if any IP has more than 5 failed logins in the last minute.

**Exercise 4 (Intermediate):** Write a script that accepts a CIDR range (like 192.168.1.0/24) and pings each host, reporting which are alive.

**Exercise 5 (Advanced):** Write a full log parser that reads an Apache/nginx access log and produces a report showing: top 10 IPs, top 10 requested URLs, HTTP status code distribution, and requests per hour.

**Exercise 6 (Advanced):** Write a script that watches a directory for new files, calculates their MD5 hash, and logs the filename, hash, and timestamp to a CSV file. Handle the case where the same file appears multiple times.

**Exercise 7 (Security):** Write a script that checks a Linux system for common misconfigurations: SUID binaries, world-writable directories, users with empty passwords, cron jobs running as root, and services listening on all interfaces. Output a formatted report.