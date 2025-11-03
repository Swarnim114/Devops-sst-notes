

### ls - List Directory Contents

Lists files and directories in the current or specified location.

**Basic Syntax:**
```bash
ls [options] [path]
```

**Useful Flags:**

| Flag | Description |
|------|-------------|
| `-l` | Long format - shows permissions, owner, size, date |
| `-a` | Show all files including hidden files (starting with .) |
| `-h` | Human-readable sizes (KB, MB, GB) |
| `-R` | Recursive - list subdirectories |
| `-t` | Sort by modification time, newest first |
| `-r` | Reverse order |
| `-S` | Sort by file size |
| `-i` | Show inode numbers |
| `-d` | List directories themselves, not their contents |
| `-1` | One file per line |

**Common Combinations:**
```bash
ls -la          # Long format with hidden files
ls -lh          # Long format with human-readable sizes
ls -ltr         # Long format, sorted by time, reversed (oldest first)
ls -laSh        # All files, sorted by size, human-readable
```

**Examples:**
```bash
ls              # List current directory
ls /home        # List /home directory
ls -la          # Detailed view including hidden files
ls -lh *.txt    # List all .txt files with readable sizes
```

---

### cd - Change Directory

Navigate between directories in the file system.

**Basic Syntax:**
```bash
cd [path]
```

**Special Shortcuts:**

| Command | Action |
|---------|--------|
| `cd` or `cd ~` | Go to home directory |
| `cd -` | Go to previous directory |
| `cd ..` | Go up one directory level |
| `cd ../..` | Go up two directory levels |
| `cd /` | Go to root directory |
| `cd ./folder` | Go to folder in current directory |

**Examples:**
```bash
cd /var/log              # Absolute path
cd Documents             # Relative path
cd ~/Downloads           # Home directory path
cd ../../etc             # Go up two levels then to etc
```

---

## File and Directory Management

### mkdir - Make Directory

Create new directories.

**Basic Syntax:**
```bash
mkdir [options] directory_name
```

**Useful Flags:**

| Flag | Description |
|------|-------------|
| `-p` | Create parent directories as needed |
| `-v` | Verbose - print message for each created directory |
| `-m` | Set permissions mode |

**Examples:**
```bash
mkdir newfolder
mkdir -p parent/child/grandchild    # Creates all nested directories
mkdir -v folder1 folder2 folder3    # Create multiple with messages
mkdir -m 755 publicfolder           # Create with specific permissions
```

---

### rmdir - Remove Directory

Remove empty directories only.

**Basic Syntax:**
```bash
rmdir [options] directory_name
```

**Useful Flags:**

| Flag | Description |
|------|-------------|
| `-p` | Remove directory and parent directories if empty |
| `-v` | Verbose mode |

**Examples:**
```bash
rmdir emptyfolder
rmdir -p parent/child/grandchild    # Removes all if empty
```

**Note:** `rmdir` only works on empty directories. For non-empty directories, use `rm -r`.

---

### rm - Remove Files and Directories

Delete files and directories.

**Basic Syntax:**
```bash
rm [options] file_or_directory
```

**Useful Flags:**

| Flag | Description |
|------|-------------|
| `-r` or `-R` | Recursive - remove directories and contents |
| `-f` | Force - ignore nonexistent files, no confirmation |
| `-i` | Interactive - prompt before every removal |
| `-v` | Verbose - explain what is being done |
| `-d` | Remove empty directories |

**Common Combinations:**
```bash
rm -rf          # Force recursive removal (DANGEROUS)
rm -ri          # Recursive with confirmation
rm -rfv         # Force recursive with verbose output
```

**Examples:**
```bash
rm file.txt                  # Remove single file
rm file1.txt file2.txt       # Remove multiple files
rm *.log                     # Remove all .log files
rm -r folder                 # Remove directory and contents
rm -rf /path/to/folder       # Force remove without confirmation
rm -i *.txt                  # Interactive removal of .txt files
```

**Warning:** `rm -rf` is extremely dangerous and can delete everything without confirmation. Always double-check before using it.

---

### touch - Create Empty File or Update Timestamp

Create empty files or update file timestamps.

**Basic Syntax:**
```bash
touch [options] filename
```

**Useful Flags:**

| Flag | Description |
|------|-------------|
| `-a` | Change only access time |
| `-m` | Change only modification time |
| `-c` | Do not create file if it doesn't exist |
| `-t` | Use specified timestamp |
| `-r` | Use timestamp from reference file |

**Examples:**
```bash
touch newfile.txt                    # Create empty file
touch file1.txt file2.txt file3.txt  # Create multiple files
touch -c existingfile.txt            # Update timestamp only if exists
touch -t 202301151200 file.txt       # Set specific timestamp
```

---

## Search and Filter Commands

### find - Search for Files and Directories

Search for files and directories in a directory hierarchy.

**Basic Syntax:**
```bash
find [path] [options] [expression]
```

**Useful Options:**

| Option | Description |
|--------|-------------|
| `-name` | Search by filename (case-sensitive) |
| `-iname` | Search by filename (case-insensitive) |
| `-type f` | Find files only |
| `-type d` | Find directories only |
| `-size` | Search by file size |
| `-mtime` | Modified time in days |
| `-atime` | Access time in days |
| `-user` | Find by owner username |
| `-group` | Find by group name |
| `-perm` | Find by permissions |
| `-empty` | Find empty files or directories |
| `-delete` | Delete found files |
| `-exec` | Execute command on found files |

**Size Modifiers:**
- `c` - bytes
- `k` - kilobytes
- `M` - megabytes
- `G` - gigabytes

**Time Modifiers:**
- `+n` - more than n days ago
- `-n` - less than n days ago
- `n` - exactly n days ago

**Examples:**
```bash
find . -name "*.txt"                    # Find all .txt files
find /home -iname "readme*"             # Case-insensitive search
find . -type f -name "*.log"            # Find only .log files
find . -type d -name "test*"            # Find only directories
find . -size +100M                      # Files larger than 100MB
find . -size -1k                        # Files smaller than 1KB
find . -mtime -7                        # Modified in last 7 days
find . -mtime +30                       # Modified more than 30 days ago
find . -user john                       # Files owned by john
find . -perm 644                        # Files with 644 permissions
find . -empty                           # Empty files and directories
find . -name "*.tmp" -delete            # Find and delete .tmp files
find . -type f -exec chmod 644 {} \;    # Change permissions
find . -name "*.txt" -exec grep "error" {} \;  # Search in files
```

---

### grep - Search Text Patterns

Search for patterns in files or output.

**Basic Syntax:**
```bash
grep [options] pattern [file...]
```

**Useful Flags:**

| Flag | Description |
|------|-------------|
| `-i` | Ignore case |
| `-r` or `-R` | Recursive search in directories |
| `-v` | Invert match (show non-matching lines) |
| `-n` | Show line numbers |
| `-c` | Count matching lines |
| `-l` | Show only filenames with matches |
| `-L` | Show only filenames without matches |
| `-w` | Match whole words only |
| `-x` | Match whole lines only |
| `-A n` | Show n lines after match |
| `-B n` | Show n lines before match |
| `-C n` | Show n lines before and after match |
| `-o` | Show only matching part |
| `-E` | Extended regex (same as egrep) |
| `-F` | Fixed strings (same as fgrep) |
| `--color` | Highlight matches |

**Examples:**
```bash
grep "error" log.txt                    # Search for "error" in file
grep -i "error" log.txt                 # Case-insensitive search
grep -r "TODO" .                        # Recursive search in current dir
grep -rn "function" *.js                # Recursive with line numbers
grep -v "debug" log.txt                 # Lines NOT containing "debug"
grep -c "warning" log.txt               # Count matching lines
grep -l "import" *.py                   # Files containing "import"
grep -w "test" file.txt                 # Match whole word "test"
grep -A 3 "error" log.txt               # Show 3 lines after match
grep -B 2 "error" log.txt               # Show 2 lines before match
grep -C 2 "error" log.txt               # Show 2 lines before and after
grep "^start" file.txt                  # Lines starting with "start"
grep "end$" file.txt                    # Lines ending with "end"
grep -E "error|warning" log.txt         # Multiple patterns (OR)
ps aux | grep python                    # Search in command output
```

---

## Process Management

### ps - Process Status

Display information about running processes.

**Basic Syntax:**
```bash
ps [options]
```

**Useful Flag Combinations:**

| Command | Description |
|---------|-------------|
| `ps` | Show processes for current shell |
| `ps aux` | Show all processes (BSD style) |
| `ps -ef` | Show all processes (Unix style) |
| `ps -u username` | Show processes for specific user |
| `ps -p PID` | Show specific process by PID |
| `ps -C command` | Show processes by command name |

**Flag Details:**

| Flag | Description |
|------|-------------|
| `a` | Show processes for all users |
| `u` | Display user-oriented format |
| `x` | Show processes without controlling terminal |
| `-e` | Select all processes |
| `-f` | Full format listing |
| `-l` | Long format |
| `-p` | Select by process ID |
| `-u` | Select by user |
| `-C` | Select by command name |
| `--sort` | Sort by column |

**Output Columns:**
- **USER** - Process owner
- **PID** - Process ID
- **%CPU** - CPU usage percentage
- **%MEM** - Memory usage percentage
- **VSZ** - Virtual memory size
- **RSS** - Resident set size (physical memory)
- **TTY** - Terminal type
- **STAT** - Process state
- **START** - Start time
- **TIME** - CPU time used
- **COMMAND** - Command name

**Process States:**
- `R` - Running
- `S` - Sleeping
- `D` - Uninterruptible sleep
- `Z` - Zombie
- `T` - Stopped

**Examples:**
```bash
ps                              # Current shell processes
ps aux                          # All processes, detailed
ps -ef                          # All processes, full format
ps aux | grep python            # Find python processes
ps -u john                      # Processes owned by john
ps -p 1234                      # Process with PID 1234
ps -C nginx                     # All nginx processes
ps aux --sort=-%mem             # Sort by memory usage
ps aux --sort=-%cpu             # Sort by CPU usage
ps -eo pid,user,cmd             # Custom column output
```

---

### man - Manual Pages

Display manual pages for commands.

**Basic Syntax:**
```bash
man [section] command
```

**Manual Sections:**
1. User commands
2. System calls
3. Library functions
4. Special files
5. File formats
6. Games
7. Miscellaneous
8. System administration commands
9. Kernel routines

**Navigation in man Pages:**
- **Space** - Next page
- **b** - Previous page
- **/** - Search forward
- **?** - Search backward
- **n** - Next search result
- **N** - Previous search result
- **q** - Quit
- **h** - Help

**Examples:**
```bash
man ls                  # Manual for ls command
man 1 printf            # Section 1 printf (command)
man 3 printf            # Section 3 printf (C function)
man -k search_term      # Search manual descriptions
man -f command          # Show brief description
man ps                  # Manual for ps command
```

---

## Users and Groups

### Understanding Users and Groups

Linux is a multi-user system where each user has a unique identity and files/processes belong to users and groups.

**User Types:**
- **Root (Superuser)** - UID 0, has full system access
- **System Users** - UID 1-999, used by services and daemons
- **Regular Users** - UID 1000+, normal user accounts

**Key Concepts:**

**User Attributes:**
- **Username** - Login name
- **UID** - User ID number
- **GID** - Primary group ID
- **Home Directory** - User's personal directory
- **Shell** - Default command interpreter

**Groups:**
- Collections of users
- Users can belong to multiple groups
- Each user has one primary group
- Groups control shared access to files

**Important Files:**
- `/etc/passwd` - User account information
- `/etc/shadow` - Encrypted passwords
- `/etc/group` - Group information
- `/etc/gshadow` - Secure group information

**Common Commands:**

```bash
whoami                  # Show current username
id                      # Show user ID and groups
id username             # Show info for specific user
groups                  # Show current user's groups
groups username         # Show groups for specific user
who                     # Show logged in users
w                       # Show who is logged in and what they're doing
```

**User Management Commands (require root/sudo):**

```bash
useradd username        # Create new user
userdel username        # Delete user
usermod -aG group user  # Add user to group
passwd username         # Change user password
groupadd groupname      # Create new group
groupdel groupname      # Delete group
```

**Examples:**
```bash
id                              # uid=1000(john) gid=1000(john) groups=1000(john),27(sudo)
groups                          # john sudo docker
groups alice                    # alice developers
```

---

## File Permissions

### Understanding File Permissions

Every file and directory has permissions that control who can read, write, or execute it.

**Permission Types:**
- **r** - Read (4)
- **w** - Write (2)
- **x** - Execute (1)

**Permission Categories:**
- **Owner (User)** - File creator/owner
- **Group** - Users in the file's group
- **Others** - Everyone else

**Permission Display:**
```
-rwxr-xr--
│││││││││└─ Others: read only
││││││└└└─ Group: read and execute
│└└└└└──── Owner: read, write, execute
└────────── File type (- = file, d = directory, l = link)
```

**Numeric (Octal) Permissions:**

Each permission set is a sum of:
- Read = 4
- Write = 2
- Execute = 1

| Number | Permission | Calculation |
|--------|------------|-------------|
| 0 | --- | 0+0+0 |
| 1 | --x | 0+0+1 |
| 2 | -w- | 0+2+0 |
| 3 | -wx | 0+2+1 |
| 4 | r-- | 4+0+0 |
| 5 | r-x | 4+0+1 |
| 6 | rw- | 4+2+0 |
| 7 | rwx | 4+2+1 |

**Common Permission Patterns:**

| Mode | Numeric | Description |
|------|---------|-------------|
| -rw------- | 600 | Owner read/write only |
| -rw-r--r-- | 644 | Owner read/write, others read |
| -rwx------ | 700 | Owner full access only |
| -rwxr-xr-x | 755 | Owner full, others read/execute |
| -rwxrwxrwx | 777 | Everyone full access (dangerous) |
| drwxr-xr-x | 755 | Standard directory permissions |

---

### chmod - Change File Permissions

Modify file and directory permissions.

**Basic Syntax:**
```bash
chmod [options] mode file
```

**Two Methods:**

**1. Symbolic Mode:**

Syntax: `[who][operator][permissions]`

**Who:**
- `u` - User (owner)
- `g` - Group
- `o` - Others
- `a` - All (ugo)

**Operators:**
- `+` - Add permission
- `-` - Remove permission
- `=` - Set exact permission

**Permissions:**
- `r` - Read
- `w` - Write
- `x` - Execute

**2. Numeric (Octal) Mode:**

Three digits representing owner, group, and others.

**Useful Flags:**

| Flag | Description |
|------|-------------|
| `-R` | Recursive - apply to directories and contents |
| `-v` | Verbose - show files being processed |
| `-c` | Show only changes made |
| `--reference=file` | Use permissions from reference file |

**Examples - Symbolic Mode:**
```bash
chmod u+x file.sh               # Add execute for owner
chmod g-w file.txt              # Remove write for group
chmod o+r file.txt              # Add read for others
chmod a+x script.sh             # Add execute for all
chmod u+rw,g+r,o-rwx file.txt   # Multiple changes
chmod u=rwx,g=rx,o=r file.txt   # Set exact permissions
chmod +x script.sh              # Add execute for all (shorthand)
```

**Examples - Numeric Mode:**
```bash
chmod 644 file.txt              # rw-r--r--
chmod 755 script.sh             # rwxr-xr-x
chmod 700 private.txt           # rwx------
chmod 666 shared.txt            # rw-rw-rw-
chmod 777 public.sh             # rwxrwxrwx (avoid this)
chmod 600 ~/.ssh/id_rsa         # Secure SSH key
chmod 400 secrets.txt           # Read-only for owner
```

**Examples - Recursive:**
```bash
chmod -R 755 /var/www/html      # Set directory permissions
chmod -R u+w folder             # Add write recursively
chmod -R go-rwx private_folder  # Remove all group/other permissions
```

**Special Permissions:**

| Mode | Numeric | Description |
|------|---------|-------------|
| Setuid | 4000 | Run as file owner |
| Setgid | 2000 | Run as file group |
| Sticky bit | 1000 | Only owner can delete (for directories) |

**Examples - Special Permissions:**
```bash
chmod 4755 binary               # Setuid
chmod 2755 directory            # Setgid
chmod 1777 /tmp                 # Sticky bit
chmod u+s file                  # Add setuid
chmod g+s directory             # Add setgid
chmod +t directory              # Add sticky bit
```

**Related Commands:**

```bash
chown user file                 # Change file owner
chown user:group file           # Change owner and group
chgrp group file                # Change file group
chown -R user:group directory   # Recursive ownership change
```

**Best Practices:**
- Never use 777 unless absolutely necessary
- Use 755 for directories and executables
- Use 644 for regular files
- Use 600 for sensitive files (passwords, keys)
- Use 400 for read-only secrets
- Always test permission changes on non-critical files first

---

## Quick Reference Table

| Command | Purpose | Most Common Usage |
|---------|---------|-------------------|
| `ls -la` | List all files with details | Directory inspection |
| `cd ~` | Go to home directory | Navigation |
| `mkdir -p` | Create nested directories | Directory creation |
| `rm -rf` | Force remove directories | Deletion (use carefully) |
| `touch` | Create empty file | File creation |
| `find . -name` | Search for files | File searching |
| `grep -r` | Search text recursively | Text searching |
| `ps aux` | Show all processes | Process monitoring |
| `chmod 755` | Set execute permissions | Permission management |
| `man command` | Read manual | Learning commands |

---

## Tips and Best Practices

**Safety Tips:**
- Always use `rm -i` for important deletions
- Double-check paths before using `rm -rf`
- Test `find` commands with `-print` before using `-delete`
- Use `ls` to verify paths before operating on them

**Efficiency Tips:**
- Use tab completion to avoid typos
- Use `!!` to repeat last command
- Use `!$` to reference last argument
- Use `Ctrl+R` to search command history
- Create aliases for frequent commands

**Common Aliases:**
```bash
alias ll='ls -lah'
alias la='ls -A'
alias l='ls -CF'
alias grep='grep --color=auto'
alias ..='cd ..'
alias ...='cd ../..'
```

---

## Related Topics to Explore

- **Text Processing:** cat, less, more, head, tail, sed, awk
- **File Operations:** cp, mv, ln, tar, zip, unzip
- **System Monitoring:** top, htop, df, du, free
- **Network:** ping, curl, wget, netstat, ss
- **Package Management:** apt, yum, pacman, dnf
- **Process Control:** kill, killall, pkill, bg, fg, jobs
- **Text Editors:** nano, vim, emacs

---

## Additional Resources

- Use `man command` for detailed documentation
- Use `command --help` for quick usage summary
- Use `info command` for detailed info pages
- Online: tldr.sh for simplified examples
- Practice in a safe environment or test directory


# Shell Scripting Basics

## Shells

A shell is a command-line interpreter that runs commands. It's the interface between you and the operating system.

**Common Shells:**
- Bash - `/bin/bash` (most common)
- Zsh - `/bin/zsh`
- Sh - `/bin/sh`

**Check your shell:**
```bash
echo $SHELL
```

---

## Shebang

The shebang `#!` is the first line of a script. It tells the system which interpreter to use.

**Syntax:**
```bash
#!/bin/bash
```

**Common shebangs:**
```bash
#!/bin/bash              # Bash script
#!/bin/sh                # Shell script
#!/usr/bin/env bash      # Portable bash
```

**Example:**
```bash
#!/bin/bash
echo "Hello"
```

---

## Echo Command

Print text to terminal.

**Basic usage:**
```bash
echo "Hello World"
echo Hello World
```

**With variables:**
```bash
NAME="John"
echo "Hello, $NAME"
```

---

## Print "World" Script

**File: hello.sh**
```bash
#!/bin/bash
echo "World"
```

**Run it:**
```bash
chmod +x hello.sh
./hello.sh
```

---

## Check If File Exists

**Basic script:**
```bash
#!/bin/bash

FILE="myfile.txt"

if [ -e "$FILE" ]; then
    echo "File exists"
else
    echo "File does not exist"
fi
```

**Common checks:**
- `-e` - File exists
- `-f` - Regular file exists
- `-d` - Directory exists

**Example with input:**
```bash
#!/bin/bash

FILE="$1"

if [ -f "$FILE" ]; then
    echo "$FILE exists"
else
    echo "$FILE does not exist"
fi
```

**Usage:**
```bash
./check_file.sh myfile.txt
```

---

## Create User Script

**File: create_user.sh**
```bash
#!/bin/bash

if [ "$EUID" -ne 0 ]; then
    echo "Run as root"
    exit 1
fi

USERNAME="$1"

if [ -z "$USERNAME" ]; then
    echo "Usage: $0 <username>"
    exit 1
fi

useradd -m "$USERNAME"
passwd "$USERNAME"

echo "User $USERNAME created"
```

**Usage:**
```bash
sudo ./create_user.sh john
```

---

## Create Group Script

**File: create_group.sh**
```bash
#!/bin/bash

if [ "$EUID" -ne 0 ]; then
    echo "Run as root"
    exit 1
fi

GROUPNAME="$1"

if [ -z "$GROUPNAME" ]; then
    echo "Usage: $0 <groupname>"
    exit 1
fi

groupadd "$GROUPNAME"

echo "Group $GROUPNAME created"
```

**Usage:**
```bash
sudo ./create_group.sh developers
```

---

## Create User and Add to Group

**File: user_with_group.sh**
```bash
#!/bin/bash

if [ "$EUID" -ne 0 ]; then
    echo "Run as root"
    exit 1
fi

USERNAME="$1"
GROUPNAME="$2"

if [ -z "$USERNAME" ] || [ -z "$GROUPNAME" ]; then
    echo "Usage: $0 <username> <groupname>"
    exit 1
fi

# Create group if doesn't exist
if ! getent group "$GROUPNAME" &>/dev/null; then
    groupadd "$GROUPNAME"
fi

# Create user
useradd -m -g "$GROUPNAME" "$USERNAME"
passwd "$USERNAME"

echo "User $USERNAME created in group $GROUPNAME"
```

**Usage:**
```bash
sudo ./user_with_group.sh john developers
```

---

## Quick Reference

**Run a script:**
```bash
chmod +x script.sh
./script.sh
```

**Pass arguments:**
```bash
./script.sh arg1 arg2
# $1 = arg1, $2 = arg2
```

**Check if root:**
```bash
if [ "$EUID" -ne 0 ]; then
    echo "Run as root"
    exit 1
fi
```

**Check if variable is empty:**
```bash
if [ -z "$VAR" ]; then
    echo "Variable is empty"
fi
```