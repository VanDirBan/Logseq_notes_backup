- #Commands
	- Command Line
		- `ls` - list files
		- `cd` - change directory
		- `pwd` - print working directory
	- File Management
		- `cp` - copy files
		- `mv` - move files
		- `rm` - remove files
		- `touch` - create an empty file or update the timestamp of an existing file
			- `touch ~/.ssh/authorized_keys` - create an empty `authorized_keys` file in the user's `.ssh` directory
		- **`chmod` (Change Mode)**
			- **Definition**:
				- A command used to change file and directory permissions in Linux.
			- **Types of Owners**:
				- **User (u)**: The owner of the file.
				- **Group (g)**: Users in the group that owns the file.
				- **Others (o)**: All other users.
			- **Types of Permissions**:
				- **r (read)**: Read permission.
				- **w (write)**: Write permission.
				- **x (execute)**: Execute permission.
			- **Permission Formats**:
				- **Symbolic Format**: Uses characters (`r`, `w`, `x`) for permissions.
					- Example: `chmod u+x file.txt` (adds execute permission for the owner).
				- **Octal Format**: Uses numeric values for permissions.
					- `4` = read, `2` = write, `1` = execute.
					- Example: `chmod 755 script.sh` (owner gets `rwx`, group and others get `r-x`).
			- **Examples**:
				- **Set read, write, and execute for all**:
				  ```sh
				  chmod 777 file.txt
				  ```
				- **Set read and execute for all**:
				  ```sh
				  chmod 755 script.sh
				  ```
				- **Add write permission for group**:
				  ```sh
				  chmod g+w file.txt
				  ```
				- **Remove execute permission for others**:
				  ```sh
				  chmod o-x file.txt
				  ```
				- **Recursive permission change**:
				  ```sh
				  chmod -R 755 /var/www/html
				  ```
				- **Set symbolic permissions for all owners**:
				  ```sh
				  chmod u=rwx,g=rx,o=r file.txt
				  ```
	- **`du` (Disk Usage)**
		- **Definition**:
			- A command-line utility that estimates and displays disk space usage of files and directories (by default, based on allocated disk blocks).
		- **Basic Functions**:
			- Shows disk usage for files and directory trees.
			- Helps identify large (“heavy”) directories.
			- Supports human-readable units, depth limits, totals, and filesystem boundary control.
		- **Common Options**:
			- **Human-readable**: `-h` (KiB/MiB/GiB)
			- **Summarize (total only)**: `-s`
			- **Limit depth**: `--max-depth=N`
			- **Grand total**: `-c`
			- **Apparent size** (logical size, not allocated blocks): `--apparent-size`
			- **Stay on one filesystem** (don’t cross mount points): `-x`
			- **Count inodes instead of size**: `--inodes`
			- **Exclude paths by pattern**: `--exclude='PATTERN'`
		- **Notes / Gotchas**:
			- By default, `du` reports **allocated** space, which can differ from the logical file size shown by `ls -l`.
			- Sparse files, hard links, compression/deduplication, and bind mounts can produce “surprising” results.
			- Use `--apparent-size` when you want to approximate the logical size of files.
		- **Examples**:
			- **Total size of a directory (summary)**:
			  
			  ```
			  du -sh /var/log
			  ```
			- **Show sizes of top-level subdirectories (depth = 1)**:
			  
			  ```
			  du -h --max-depth=1 /home/user
			  ```
			- **Grand total + per-item output**:
			  
			  ```
			  du -h --max-depth=1 -c /srv
			  ```
			- **Find the largest directories (top 10)**:
			  
			  ```
			  du -h --max-depth=1 /var | sort -hr | head -n 10
			  ```
			- **Do not cross filesystem boundaries (stay on one mount)**:
			  
			  ```
			  du -xh --max-depth=1 /
			  ```
			- **Exclude patterns (e.g., caches, node_modules)**:
			  
			  ```
			  du -h --max-depth=2 --exclude='node_modules' --exclude='*.log' .
			  ```
			- **Apparent size vs allocated size**:
			  
			  ```
			  du -sh .
			  du -sh --apparent-size .
			  ```
			- **Count inodes (number of files/dirs)**:
			  
			  ```
			  du --inodes -s /home/user
			  ```
	- **`find`**
		- **Definition**:
			- A command-line utility for searching files and directories by criteria (name, type, size, time, permissions, owner, etc.) and optionally performing actions on the matches.
		- **Basic Functions**:
			- Recursively traverses directories starting from a given path.
			- Filters results using tests (`-name`, `-type`, `-size`, `-mtime`, `-perm`, …).
			- Executes actions on matches (`-print`, `-delete`, `-exec`, `-ok`, …).
		- **Core Syntax**:
			- **General form**:
			  
			  ```
			  find [PATH...] [TESTS...] [ACTIONS...]
			  ```
			- **Typical default action**: `-print` (prints matching paths).
		- **Common Tests (Filters)**:
			- **By name**:
				- `-name "PATTERN"` (case-sensitive)
				- `-iname "PATTERN"` (case-insensitive)
			- **By type**:
				- `-type f` (regular file), `-type d` (directory), `-type l` (symlink)
			- **By size**:
				- `-size +100M` (greater than 100 MB)
				- `-size -10k` (less than 10 KB)
				- `-size 1G` (exactly ~1 GiB, in find’s units)
			- **By time**:
				- `-mtime -7` (modified within last 7 days)
				- `-mmin -60` (modified within last 60 minutes)
				- `-ctime` / `-cmin` (metadata change time)
				- `-atime` / `-amin` (last access time)
			- **By ownership**:
				- `-user USER`, `-group GROUP`
			- **By permissions**:
				- `-perm 644` (exact)
				- `-perm -644` (at least these bits set)
				- `-perm /222` (any of these bits set; e.g., writable by someone)
			- **Depth control**:
				- `-maxdepth N`, `-mindepth N`
		- **Common Actions**:
			- **Print**:
				- `-print` (default), `-print0` (NUL-delimited; safe for weird filenames)
			- **Execute a command**:
				- `-exec CMD {} \;` (runs once per match)
				- `-exec CMD {} +` (packs many matches per run; faster)
				- `-ok CMD {} \;` (asks confirmation)
			- **Delete**:
				- `-delete` (dangerous; prefer dry-run first)
		- **Operators / Logic**:
			- **AND**: implicit between tests (e.g., `-type f -name "*.log"`).
			- **OR**: `-o` (use parentheses to control precedence).
			- **NOT**: `!` or `-not`.
			- **Grouping**:
				- Use escaped parentheses in shell: `\( ... \)`
		- **Notes / Gotchas**:
			- Prefer `-print0` + `xargs -0` when piping to avoid breaking on spaces/newlines.
			- `-exec ... {} +` is usually much faster than `-exec ... {} \;`.
			- `-delete` obeys the expression order; put tests **before** `-delete`.
			- If you need to match dotfiles, patterns like `"*"` already include them in `find` (unlike shell globs), but `-name ".*"` matches only dotfiles.
		- **Examples**:
			- **Find files by name in the current directory tree**:
			  
			  ```
			  find . -name "app.conf"
			  ```
			- **Case-insensitive name search**:
			  
			  ```
			  find /etc -iname "*ssh*"
			  ```
			- **Find directories only**:
			  
			  ```
			  find . -type d -name ".git"
			  ```
			- **Find files larger than 100 MB**:
			  
			  ```
			  find /var -type f -size +100M
			  ```
			- **Find files modified in the last 24 hours**:
			  
			  ```
			  find . -type f -mtime -1
			  ```
			- **Find and delete `*.tmp` files (dry-run first!)**:
			  
			  ```
			  find . -type f -name "*.tmp" -print
			  find . -type f -name "*.tmp" -delete
			  ```
			- **Find `*.log` and execute a command (once per file)**:
			  
			  ```
			  find /var/log -type f -name "*.log" -exec gzip {} \;
			  ```
			- **Same, but faster (batch execution)**:
			  
			  ```
			  find /var/log -type f -name "*.log" -exec gzip {} +
			  ```
			- **Use OR with grouping**:
			  
			  ```
			  find . \( -name "*.jpg" -o -name "*.png" \) -type f
			  ```
			- **Safe piping with NUL delimiters**:
			  
			  ```
			  find . -type f -name "*.txt" -print0 | xargs -0 wc -l
			  ```
			- **Find files writable by anyone (permission bits)**:
			  
			  ```
			  find /srv -type f -perm /222
			  ```
	-
	-
	-
	-
	- **`uniq`**
		- **Definition**:
			- A command-line utility that filters out (or reports) repeated adjacent lines in text input.
		- **Basic Functions**:
			- Removes duplicate lines **only when they are adjacent**.
			- Can count occurrences, show only duplicates, or show only unique lines.
			- Commonly paired with `sort` to remove duplicates from unsorted input.
		- **Core Syntax**:
			- ```
			  uniq [OPTIONS]... [INPUT [OUTPUT]]
			  ```
		- **Important Behavior**:
			- `uniq` compares **neighboring** lines only.
			- If duplicates are not next to each other, `uniq` will not remove them unless you sort first:
			  
			  ```
			  sort file.txt | uniq
			  ```
		- **Common Options**:
			- **Count occurrences**: `-c`
			- **Only duplicates** (repeated lines): `-d`
			- **Only unique lines** (appear once): `-u`
			- **Ignore case**: `-i`
			- **Skip first N fields**: `-f N` (fields are whitespace-delimited)
			- **Skip first N characters**: `-s N`
			- **Compare at most N characters**: `-w N`
		- **Notes / Gotchas**:
			- `uniq` is for *adjacent* duplicates; `sort -u` is often a one-liner alternative:
				- `sort -u file.txt` = sort + deduplicate
			- If you need to deduplicate lines while preserving original order, `uniq` won’t help unless duplicates are already adjacent (you’d use other tools like `awk`).
		- **Examples**:
			- **Remove adjacent duplicates**:
			  
			  ```
			  printf "a\na\nb\nb\nb\nc\n" | uniq
			  ```
			- **Count duplicates**:
			  
			  ```
			  printf "a\na\nb\nb\nb\nc\n" | uniq -c
			  ```
			- **Show only duplicated lines**:
			  
			  ```
			  printf "a\na\nb\nb\nb\nc\n" | uniq -d
			  ```
			- **Show only lines that appear once**:
			  
			  ```
			  printf "a\na\nb\nb\nb\nc\n" | uniq -u
			  ```
			- **Deduplicate an unsorted file (sort first)**:
			  
			  ```
			  sort hosts.txt | uniq
			  ```
			- **Count frequency of each line (classic pattern)**:
			  
			  ```
			  sort access.log | uniq -c | sort -nr | head -n 20
			  ```
			- **Ignore case while deduplicating**:
			  
			  ```
			  sort words.txt | uniq -i
			  ```
			- **Deduplicate by ignoring the first field** (e.g., ignore timestamps):
			  
			  ```
			  sort data.txt | uniq -f 1
			  ```
	- **`sort`**
		- **Definition**:
			- A command-line utility that sorts lines of text from files or standard input and outputs the sorted result.
		- **Basic Functions**:
			- Sorts lines alphabetically (default: lexicographic, by byte/locale).
			- Supports numeric sorting, reverse order, unique filtering, and key/field-based sorting.
			- Commonly used in pipelines to organize command output.
		- **Core Syntax**:
			- ```
			  sort [OPTIONS]... [FILE]...
			  ```
		- **Common Options**:
			- **Reverse order**: `-r`
			- **Numeric sort**: `-n` (compares numbers by value)
			- **Human-numeric sort** (e.g., `1K`, `10M`, `2G`): `-h`
			- **Unique lines**: `-u`
			- **Check if already sorted**: `-c` (exit non-zero if not sorted)
			- **Ignore leading blanks**: `-b`
			- **Ignore case**: `-f`
			- **General numeric sort** (handles decimals, exponents): `-g`
			- **Version sort** (e.g., `v1.9` < `v1.10`): `-V`
			- **Specify field delimiter**: `-t 'DELIM'`
			- **Sort by key/field**: `-k START[,END]`
			- **Output to file**: `-o FILE`
		- **Key / Field Sorting**:
			- Fields are defined by a delimiter (`-t`), default is whitespace.
			- `-k 2,2` means “sort using only field 2”.
			- You can set start/end positions to limit what part of the line is used as the key.
		- **Notes / Gotchas**:
			- Locale affects ordering; for consistent byte-order sorting, set:
			  
			  ```
			  LC_ALL=C sort ...
			  ```
			- `sort -u` is not identical to `uniq` in all cases:
				- `uniq` removes adjacent duplicates (input should already be sorted).
				- `sort -u` sorts and removes duplicates in one step.
			- `-h` is ideal for sizes like `10K`, `2G`, but relies on valid suffixes.
			- `-n` vs `-g`:
				- `-n` numeric (simple) is usually enough.
				- `-g` supports floating point and scientific notation more reliably.
		- **Examples**:
			- **Sort a file alphabetically**:
			  
			  ```
			  sort names.txt
			  ```
			- **Sort in reverse**:
			  
			  ```
			  sort -r names.txt
			  ```
			- **Numeric sort**:
			  
			  ```
			  printf "10\n2\n33\n" | sort -n
			  ```
			- **Sort human-readable sizes**:
			  
			  ```
			  printf "1K\n900\n2M\n10K\n" | sort -h
			  ```
			- **Remove duplicates (sort + unique)**:
			  
			  ```
			  sort -u hosts.txt
			  ```
			- **Check if a file is already sorted**:
			  
			  ```
			  sort -c data.txt
			  ```
			- **Sort by second column (whitespace-delimited)**:
			  
			  ```
			  sort -k 2,2 table.txt
			  ```
			- **Sort by 3rd column using comma delimiter**:
			  
			  ```
			  sort -t ',' -k 3,3 data.csv
			  ```
			- **Sort by numeric value in 2nd column (comma-delimited)**:
			  
			  ```
			  sort -t ',' -k 2,2n data.csv
			  ```
			- **Stable-ish results across systems (locale-independent)**:
			  
			  ```
			  LC_ALL=C sort input.txt
			  ```
			- **Sort command output: largest directories (du + sort)**:
			  
			  ```
			  du -h --max-depth=1 /var | sort -hr | head -n 10
			  ```
	- Data Filtering
		- **`grep` (Global Regular Expression Print)**
			- **Definition**:
				- A command-line utility used for searching specific patterns in text files or command output.
			- **Basic Functions**:
				- Searches for patterns in files and directories.
				- Supports regular expressions for flexible searches.
				- Filters output of other commands in real-time.
			- **Examples**:
				- **Simple Search in a File**:
				  ```sh
				  grep "error" /var/log/syslog
				  ```
				- **Case-Insensitive Search**:
				  ```sh
				  grep -i "error" /var/log/syslog
				  ```
				- **Recursive Search**:
				  ```sh
				  grep -r "main()" /home/user/code
				  ```
				- **Pattern Search with Regular Expressions**:
				  ```sh
				  grep "^[0-9]\{3\}-[0-9]\{2\}-[0-9]\{4\}" file.txt
				  ```
				- **Count Matches**:
				  ```sh
				  grep -c "user" /etc/passwd
				  ```
				- **Show Only File Names with Matches**:
				  ```sh
				  grep -l "TODO" *.c
				  ```
				- **Exclude Matching Lines**:
				  ```sh
				  grep -v "debug" /var/log/syslog
				  ```
				- **Show Context Around Matches**:
					- **Lines After Matches**: `grep -A 2 "error" /var/log/syslog`
					- **Lines Before Matches**: `grep -B 2 "error" /var/log/syslog`
					- **Lines Before and After**: `grep -C 2 "error" /var/log/syslog`
				- **Filter Output from Other Commands**:
				  ```sh
				  ps aux | grep "apache2"
				  ```
				- **Show Line Numbers for Matches**:
				  ```sh
				  grep -n "main" code.c
				  ```
		- **Regular Expressions (Regex)**
			- **Definition**:
				- A pattern of characters used for searching, matching, and manipulating text in Linux.
			- **Basic Concepts**:
				- **Characters**:
					- Literals (e.g., "abc").
					- Special characters (e.g., `.` matches any character).
				- **Modifiers**:
					- `*` (zero or more), `+` (one or more), `?` (zero or one).
				- **Anchors**:
					- `^` (start of string), `$` (end of string).
				- **Grouping and Alternation**:
					- `( )` for grouping, `|` for alternation.
				- **Character Classes and Ranges**:
					- `[abc]` (matches any character), `[a-z]` (matches any lowercase letter), `[^abc]` (matches any character except "a", "b", "c").
			- **Examples in Linux**:
				- **Using grep**: 
				  ```sh
				  grep -E "[0-9]{3}-[0-9]{2}-[0-9]{4}" file.txt
				  ```
				- **Using sed**: 
				  ```sh
				  sed -E 's/[0-9]{3}-[0-9]{2}-[0-9]{4}/XXX-XX-XXXX/g' file.txt
				  ```
				- **Using awk**: 
				  ```sh
				  awk '/[0-9]{3}-[0-9]{2}-[0-9]{4}/ {print $0}' file.txt
				  ```
			- **Common Regex Patterns**:
				- **MAC Address**:
					- Pattern: `([0-9A-Fa-f]{2}:){5}[0-9A-Fa-f]{2}`
				- **IPv4 Address**:
					- Pattern: `\b([0-9]{1,3}\.){3}[0-9]{1,3}\b`
	- Package Management
		- `pip` - package installer for Python
		- `dnf` - package manager for RPM-based distributions (e.g., Fedora)
			- `dnf repolist` - display the list of available repositories and their status (RPM-based systems)
		- `apt` - package manager for Debian-based distributions (e.g., Ubuntu)
			- `apt-cache policy` - display the list of available repositories and their priorities (Debian-based systems)
		- Ubuntu Repositories
		  collapsed:: true
			- **Main**: Officially supported packages
			- **Universe**: Community-maintained packages
			- **Multiverse**: Packages that may include non-free software
			- **Restricted**: Restricted packages like drivers
			- Adding PPAs
				- **Example**
					- Add a PPA (Personal Package Archive):
						- Command: `sudo add-apt-repository ppa:repository-name/ppa`
						- Example: `sudo add-apt-repository ppa:deadsnakes/ppa`
					- Update the package list:
						- Command: `sudo apt-get update`
					- Install a package from the PPA:
						- Command: `sudo apt-get install package-name`
						- Example: `sudo apt-get install python3.9`
				- **PHP PPA**:
					- Add PPA: `sudo add-apt-repository ppa:ondrej/php`
					- Update package list: `sudo apt-get update`
					- Install PHP: `sudo apt-get install php7.4`
				- **MySQL PPA**:
					- Add PPA: `sudo add-apt-repository 'deb http://repo.mysql.com/apt/ubuntu/ focal mysql-5.7'`
					- Update package list: `sudo apt-get update`
					- Install MySQL: `sudo apt-get install mysql-server`
	- Text Editors
		- `nano` - a simple text editor for Unix-like systems
			- `nano ~/.ssh/authorized_keys` - open the `authorized_keys` file in the user's `.ssh` directory for editing
			- `nano /root/.ssh/authorized_keys` - open the `authorized_keys` file in the root's `.ssh` directory for editing
			- `nano /etc/ssh/sshd_config` - open the SSH daemon configuration file for editing
	- System Management
		- `systemctl` - control the systemd system and service manager
			- `systemctl daemon-reload`- Reload systemd configuration
			- `systemctl start [service]` - start a service
			- `systemctl stop [service]` - stop a service
			- `systemctl restart [service]` - restart a service
			- `systemctl status [service]` - check the status of a service
			- `systemctl enable [service]` - enable a service to start at boot
			- `systemctl disable [service]` - disable a service from starting at boot
		- **Unit** - A logical representation of a system resource or service managed by `systemd`.
			- **Types of Units**:
				- **Service Unit** (`.service`): Represents a service (daemon).
				- **Socket Unit** (`.socket`): Represents a network socket.
				- **Device Unit** (`.device`): Represents a device recognized by the system.
				- **Mount Unit** (`.mount`): Represents a mount point.
				- **Automount Unit** (`.automount`): Represents an automount point.
				- **Swap Unit** (`.swap`): Represents a swap partition.
				- **Target Unit** (`.target`): Represents a group of units.
				- **Timer Unit** (`.timer`): Represents a timer for scheduling tasks.
				- **Path Unit** (`.path`): Represents a file or directory to monitor for changes.
				- **Slice Unit** (`.slice`): Represents a group of resources (e.g., process groups).
				- **Scope Unit** (`.scope`): Represents external tasks not started by `systemd`.
			- **Example Service Unit** (`/etc/systemd/system/example.service`):
			  ```ini
			  [Unit]
			  Description=Example Service
			  After=network.target
			  
			  [Service]
			  ExecStart=/usr/bin/example-command
			  Restart=always
			  
			  [Install]
			  WantedBy=multi-user.target
			  ```
		- `apt-get` - Advanced Package Tool (stable interface for scripting)(old version)
		- `apt` - Advanced Package Tool (interactive interface)
			- `sudo apt update` - update the list of available packages
			- `sudo apt upgrade` - upgrade all installed packages
			- `sudo apt install [package_name]` - install a package
			- `sudo apt remove [package_name]` - remove a package
			- `apt search [package_name]` - search for a package
	- SSH Management
		- `ssh-keygen` - generate SSH key pairs for authentication
			- `ssh-keygen -t rsa -b 4096 -C "your_email@example.com"` - generate a new RSA key pair with 4096 bits and a specified comment
	- Network
		- `ip a` - display all IP addresses assigned to all network interfaces
		- `route -n` - display the kernel routing tables
		- `ping` - send ICMP ECHO_REQUEST to network hosts
		- `ifconfig` - configure a network interface
			- `ifconfig [interface]` - display configuration of a specific network interface
			- `ifconfig [interface] up` - activate a network interface
			- `ifconfig [interface] down` - deactivate a network interface
		- `netstat` - network statistics
			- `netstat -a` - display all active connections and listening ports
			- `netstat -r` - display the routing table
			- `netstat -i` - display network interface statistics
			- `netstat -tulnp` - show listening ports
				- `-t` - show TCP connections
				- `-u` - show UDP connections
				- `-l` - show listening ports
				- `-n` - show numeric addresses instead of resolving names
				- `-p` - show the PID and name of the program to which each socket belongs
		- `systemctl restart NetworkManager` - restart the NetworkManager service
	- Version Control
		- `git` - distributed version control system
- #User_and_Group_Management
  collapsed:: true
	- `useradd` - create a new user
		- `-c "comment"` - add a comment (usually the user's full name)
			- Example: `useradd -c "John Doe" johndoe`
		- `-d /home/dir` - set the user's home directory
			- Example: `useradd -d /home/johndoe johndoe`
		- `-e YYYY-MM-DD` - set the expiration date for the account
			- Example: `useradd -e 2023-12-31 johndoe`
		- `-g group` - set the primary group for the user
			- Example: `useradd -g users johndoe`
		- `-G groups` - set the supplementary groups for the user
			- Example: `useradd -G wheel,audio johndoe`
		- `-m` - create the user's home directory if it does not exist
			- Example: `useradd -m johndoe`
		- `-p password` - set the user's encrypted password (use with caution)
			- Example: `useradd -p $(openssl passwd -crypt 'password') johndoe`
		- `-s shell` - set the user's login shell
			- Example: `useradd -s /bin/bash johndoe`
		- `-u UID` - set the user ID for the user
			- Example: `useradd -u 1001 johndoe`
	- `groupadd` - create a new group
		- `-f` - do not fail if the group already exists
			- Example: `groupadd -f developers`
		- `-g GID` - set the group ID for the group
			- Example: `groupadd -g 1001 developers`
		- `-r` - create a system group (with a GID less than 1000)
			- Example: `groupadd -r developers`
	- `usermod` - modify a user account
		- `-c "comment"` - change the user's comment (usually full name)
			- Example: `usermod -c "John Doe" johndoe`
		- `-d /new/home/dir` - change the user's home directory
			- Example: `usermod -d /new/home/dir johndoe`
		- `-d /new/home/dir -m` - change the home directory and move current contents
			- Example: `usermod -d /new/home/dir -m johndoe`
		- `-e YYYY-MM-DD` - set the account expiration date
			- Example: `usermod -e 2024-12-31 johndoe`
		- `-g group` - change the user's primary group
			- Example: `usermod -g newgroup johndoe`
		- `-G groups` - change the user's supplementary groups (replaces current groups)
			- Example: `usermod -G group1,group2 johndoe`
		- `-aG group` - add the user to supplementary groups (does not replace current groups)
			- Example: `usermod -aG newgroup johndoe`
		- `-aG sudo user`
			- **sudo group**: In Debian-based systems, the `sudo` group is used to grant administrative privileges to its members. Users in this group can use the `sudo` command to execute commands as the superuser.
		- `-l newname` - change the user's login name
			- Example: `usermod -l newname oldname`
		- `-L` - lock the user's account
			- Example: `usermod -L johndoe`
		- `-U` - unlock the user's account
			- Example: `usermod -U johndoe`
		- `-s shell` - change the user's login shell
			- Example: `usermod -s /bin/zsh johndoe`
		- `-u UID` - change the user's user ID
			- Example: `usermod -u 1001 johndoe`
	- `passwd` - change user passwords and manage password properties
		- Change password for the current user:
			- Command: `passwd`
		- Change password for another user (requires superuser privileges):
			- Command: `sudo passwd username`
		- Lock a user account:
			- Command: `sudo passwd -l username`
		- Unlock a user account:
			- Command: `sudo passwd -u username`
		- Expire a user's password (force change on next login):
			- Command: `sudo passwd -e username`
		- Set the maximum number of days a password is valid:
			- Command: `sudo passwd -x days username`
		- Force user to change password on next login:
			- Command: `sudo passwd -f username`
	- `deluser` - remove a user account
		- Remove a user account:
			- Command: `sudo deluser username`
			- Example: `sudo deluser johndoe`
		- Remove a user account and home directory:
			- Command: `sudo deluser --remove-home username`
			- Example: `sudo deluser --remove-home johndoe`
		- Remove a user account and all files:
			- Command: `sudo deluser --remove-all-files username`
			- Example: `sudo deluser --remove-all-files johndoe`
		- Remove a user from a group:
			- Command: `sudo deluser username groupname`
			- Example: `sudo deluser johndoe sudo`
		- Force removal of a user:
			- Command: `sudo deluser --force username`
			- Example: `sudo deluser --force johndoe`
- #Programs
  collapsed:: true
	- **NetworkManager** - Service for managing network connections in Linux
		- Features: Automatic network configuration, support for various network types (Ethernet, Wi-Fi, mobile, VPN), graphical interfaces, and command-line tools.
		- **Installation**:
			- Install NetworkManager on Ubuntu:
				- Command: `sudo apt-get update`
				- Command: `sudo apt-get install network-manager`
			- Start and enable NetworkManager service:
				- Command: `sudo systemctl start NetworkManager`
				- Command: `sudo systemctl enable NetworkManager`
		- **`nmcli` (Network Manager Command Line Interface)**:
			- A command-line tool for managing network connections via NetworkManager.
			- **Key Features**:
				- Manage network connections (create, modify, delete, activate).
				- Configure network interfaces (enable, disable, view status).
				- Monitor network status and view available networks.
			- **Basic Commands**:
				- **General Status**:
					- Check NetworkManager status:
						- Command: `nmcli general status`
				- **Connection Management**:
					- List all connections:
						- Command: `nmcli connection show`
					- Add a new connection:
						- Command: `sudo nmcli connection add con-name 'dhcp' type ethernet ifname enp0s3`
					- Delete a connection:
						- Command: `sudo nmcli connection del 'dhcp'`
					- Bring a connection up:
						- Command: `nmcli connection up 'dhcp'`
					- Bring a connection down:
						- Command: `nmcli connection down 'dhcp'`
				- **Wi-Fi Management**:
					- List available Wi-Fi networks:
						- Command: `nmcli device wifi list`
					- Connect to a Wi-Fi network:
						- Command: `nmcli device wifi connect 'SSID' password 'your_password'`
				- **Device Management**:
					- List all network devices:
						- Command: `nmcli device status`
					- Enable a network device:
						- Command: `nmcli device set enp0s3 managed yes`
					- Disconnect a network device:
						- Command: `nmcli device disconnect enp0s3`
		- **nmtui** - Text user interface for NetworkManager
			- Start nmtui:
				- Command: `sudo nmtui`
			- Main options in nmtui:
				- Edit a connection
				- Activate a connection
				- Set system hostname
	- Network Management
		- `wget` - non-interactive network downloader
		- `net-tools`  -
		- **Adding a new Ethernet connection with DHCP using  `nmcli`**:
			- Command: `sudo nmcli connection add con-name 'dhcp' type ethernet ifname enp0s3`
			- **Explanation**:
				- `nmcli`: Command-line tool for interacting with NetworkManager.
				- `connection add`: Subcommand to add a new network connection.
				- `con-name 'dhcp'`: Sets the name of the new connection to 'dhcp'.
				- `type ethernet`: Specifies that the connection type is Ethernet (wired connection).
				- `ifname enp0s3`: Specifies the network interface name to which the connection will be applied (in this case, `enp0s3`).
			- **Purpose**: This command adds a new Ethernet connection named 'dhcp' to the network interface `enp0s3`, allowing it to use DHCP for obtaining an IP address and other network settings.
		- **Deleting a network connection using nmcli**:
			- Command: `sudo nmcli connection del 'dhcp'`
			- **Explanation**:
				- `nmcli`: Command-line tool for interacting with NetworkManager.
				- `connection del`: Subcommand to delete a network connection.
				- `'dhcp'`: Name of the connection to be deleted.
			- **Purpose**: This command deletes the network connection named 'dhcp'.
		- **Adding a static Ethernet connection using nmcli**:
			- Command: `nmcli connection add con-name 'static' ifname enp0s3 autoconnect no type ethernet ip4 192.168.200.52/24 gw4 192.168.200.1`
			- **Explanation**:
				- `connection add`: Subcommand to add a new network connection.
				- `con-name 'static'`: Sets the name of the new connection to 'static'.
				- `ifname enp0s3`: Specifies the network interface name (in this case, `enp0s3`).
				- `autoconnect no`: Specifies that the connection should not automatically connect on boot.
				- `type ethernet`: Specifies that the connection type is Ethernet (wired connection).
				- `ip4 192.168.200.52/24`: Sets the static IPv4 address for this connection.
				- `gw4 192.168.200.1`: Sets the gateway address for this connection.
			- **Purpose**: This command adds a new static Ethernet connection with a specified IP address and gateway.
		- **Activating a network connection using nmcli**:
			- Command: `nmcli con up 'static'`
			- **Explanation**:
				- `nmcli`: Command-line tool for interacting with NetworkManager.
				- `con up`: Subcommand to bring up (activate) a network connection.
				- `'static'`: Name of the connection to be activated.
			- **Purpose**: This command activates the network connection named 'static'.
- #Linux_Scripting
  collapsed:: true
	- **Bash (Bourne Again Shell)**
		- **Definition**:
			- A command-line shell and scripting language for UNIX-based operating systems like Linux.
		- **Uses**:
			- Provides an interface to execute commands, manage files, and configure the system.
			- Allows for scripting and automation of tasks, from simple file management to complex workflows.
		- **Basic Components**:
			- **Variables**: Store data and configuration for commands and programs.
				- Example: 
				  ```bash
				  NAME="Alice"
				  echo "Hello, $NAME"
				  ```
			- **Conditional Statements (if)**: Executes commands based on conditions.
				- Example:
				  ```bash
				  if [ -f "file.txt" ]; then
				    echo "File exists"
				  fi
				  ```
			- **Loops (for)**: Repeats a set of commands for multiple items.
				- Example:
				  ```bash
				  for i in 1 2 3; do
				    echo "Number $i"
				  done
				  ```
			- **Functions**: Group commands together for modularity and reusability.
				- Example:
				  ```bash
				  greet() {
				    echo "Hello, $1"
				  }
				  greet "Alice"
				  ```
		- **Key Commands**:
			- `echo`, `cd`, `ls`, `pwd`, `chmod`, `grep`, `sed`, `awk`.
		- **Examples**:
			- **File Backup**:
			  ```bash
			  tar -czvf backup.tar.gz /home/user/documents
			  ```
			- **Automatic Temp Files Cleanup**:
			  ```bash
			  find /tmp -type f -mtime +7 -exec rm {} \;
			  ```
		- **Running a Script**:
			- Create a script, make it executable with `chmod +x script.sh`, and run it with `./script.sh`.
- #Automation
  collapsed:: true
	- **Cron (Task Scheduler)**
		- **Definition**:
			- A Unix-based task scheduler used to automate recurring tasks by running commands or scripts at specific times or intervals.
		- **Crontab Format**:
			- Syntax: `* * * * * command`
			- **Fields**:
				- **Minute** (0–59)
				- **Hour** (0–23)
				- **Day of Month** (1–31)
				- **Month** (1–12)
				- **Day of Week** (0–7, where 0 and 7 represent Sunday)
			- **Special Characters**:
				- `*` (any value), `,` (list), `-` (range), `/` (step)
		- **Examples**:
			- **Run a script every day at 2:30 AM**:
			  ```plaintext
			  30 2 * * * /path/to/script.sh
			  ```
			- **Run a command every 5 minutes**:
			  ```plaintext
			  */5 * * * * /path/to/command
			  ```
			- **Weekly task on Sundays at noon**:
			  ```plaintext
			  0 12 * * 0 /path/to/script.sh
			  ```
			- **Monthly backup on the 1st at midnight**:
			  ```plaintext
			  0 0 1 * * /path/to/backup.sh
			  ```
		- **Management Commands**:
			- **Edit crontab**: `crontab -e`
			- **List crontab**: `crontab -l`
			- **Remove crontab**: `crontab -r`
		- **Logging**:
			- Log file location varies by system (e.g., `/var/log/syslog` or `/var/log/cron`).
			- Redirect output for debugging:
			  ```plaintext
			  0 3 * * * /path/to/script.sh >> /path/to/logfile.log 2>&1
			  ```
		- **Special Time Strings**:
			- `@reboot`: Run at system startup
				- Example:
				  ```plaintext
				  @reboot /path/to/script.sh
				  ```
			- `@daily`: Run daily at midnight
				- Example:
				  ```plaintext
				  @daily /path/to/backup.sh
				  ```
			- `@weekly`: Run weekly at midnight on Sunday
			- `@monthly`: Run monthly at midnight on the first day
- #System_Monitoring
  collapsed:: true
	- **Load Average**:
		- **Definition**: A metric that shows the average number of processes waiting for execution or CPU resources in the system. It is measured over three time intervals: 1, 5, and 15 minutes.
		- **Time Intervals**:
			- **1 minute**: Load average over the last 1 minute.
			- **5 minutes**: Load average over the last 5 minutes.
			- **15 minutes**: Load average over the last 15 minutes.
		- **Understanding Values**:
			- **1.0 on a single-core system**: The system is fully loaded (one process is running or waiting).
			- **1.0 on a multi-core system (e.g., 4 cores)**: The system is underutilized (4 cores can handle one process easily).
		- **Interpreting Values**:
			- **Less than 1.0 per core**: The system is functioning normally and is not overloaded.
			- **Equal to 1.0 per core**: The system is loaded but not overloaded.
			- **Greater than 1.0 per core**: The system is overloaded, and some processes must wait.
		- **Commands to Check Load Average**:
			- **`uptime`**:
				- Example: `uptime`
			- **`top`**:
				- Example: `top`
			- **`cat /proc/loadavg`**:
				- Example: `cat /proc/loadavg`
		- **Example Outputs**:
			- **`uptime`**:
			  ```plaintext
			  14:23:19 up 10 days,  2:53,  3 users,  load average: 0.15, 0.10, 0.05
			  ```
			- **`top`**:
			  ```plaintext
			  top - 14:23:19 up 10 days,  2:53,  3 users,  load average: 0.15, 0.10, 0.05
			  Tasks: 197 total,   1 running, 196 sleeping,   0 stopped,   0 zombie
			  %Cpu(s):  0.7 us,  0.3 sy,  0.0 ni, 99.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
			  ```
			- **`cat /proc/loadavg`**:
			  ```plaintext
			  0.15 0.10 0.05 1/197 4567
			  ```
	- **`iotop`** - A utility for monitoring I/O usage by processes in real time.
		- **Features**:
			- Displays current I/O activity for each process.
			- Filters active processes using I/O.
			- Shows cumulative I/O activity since `iotop` started.
		- **Installation**:
			- Debian-based systems:
				- Command: `sudo apt-get update`
				- Command: `sudo apt-get install iotop`
			- Red Hat-based systems:
				- Command: `sudo yum install iotop`
		- **Examples**:
			- Monitor all processes:
				- Command: `sudo iotop`
			- Filter active processes:
				- Command: `sudo iotop -o`
			- Show cumulative I/O:
				- Command: `sudo iotop -a`
	- **`iostat`** - A utility for monitoring system I/O statistics.
		- **Features**:
			- Displays CPU usage statistics.
			- Shows I/O statistics for devices and partitions.
			- Helps understand system resource utilization.
		- **Installation**:
			- Debian-based systems:
				- Command: `sudo apt-get update`
				- Command: `sudo apt-get install sysstat`
			- Red Hat-based systems:
				- Command: `sudo yum install sysstat`
		- **Examples**:
			- Basic command:
				- Command: `iostat`
			- Refresh statistics every 2 seconds:
				- Command: `iostat 2`
			- Refresh statistics every 2 seconds, 5 times:
				- Command: `iostat 2 5`
			- Display device statistics only:
				- Command: `iostat -d`
			- Display CPU statistics only:
				- Command: `iostat -c`
			- Display extended device statistics:
				- Command: `iostat -x`
			- Display statistics for a specific device:
				- Command: `iostat -d sda`
		- **Explanation of Output**:
			- **avg-cpu**: Average CPU usage statistics.
				- **%user**: Percentage of CPU time spent on user processes.
				- **%nice**: Percentage of CPU time spent on low-priority user processes.
				- **%system**: Percentage of CPU time spent on system (kernel) processes.
				- **%iowait**: Percentage of CPU time spent waiting for I/O operations.
				- **%steal**: Percentage of CPU time spent waiting for the hypervisor (in virtualized environments).
				- **%idle**: Percentage of CPU time spent idle.
			- **Device statistics**:
				- **tps**: Transactions per second (I/O operations per second).
				- **kB_read/s**: Kilobytes read per second.
				- **kB_wrtn/s**: Kilobytes written per second.
				- **kB_read**: Total kilobytes read.
				- **kB_wrtn**: Total kilobytes written.
	- **`lsof`** - A command-line utility to list open files and the processes that opened them.
		- **Features**:
			- Displays all open files in the system.
			- Filters open files by user, process, or file.
			- Monitors active network connections and sockets.
		- **Installation**:
			- Debian-based systems:
				- Command: `sudo apt-get update`
				- Command: `sudo apt-get install lsof`
			- Red Hat-based systems:
				- Command: `sudo yum install lsof`
		- **Examples**:
			- Display all open files:
				- Command: `lsof`
			- Display open files for a specific user:
				- Command: `lsof -u username`
			- Display open files for a specific process:
				- Command: `lsof -p PID`
			- Display processes that opened a specific file:
				- Command: `lsof /path/to/file`
			- Display open files in a specific directory:
				- Command: `lsof +D /path/to/directory`
			- Display network connections:
				- Command: `lsof -i`
			- Display processes using a specific port:
				- Command: `lsof -i :80`
		- **Example Output**:
		  ```plaintext
		  COMMAND     PID    USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
		  init          1    root  cwd    DIR  259,1     4096    2 /
		  init          1    root  rtd    DIR  259,1     4096    2 /
		  init          1    root  txt    REG  259,1  1599080 4373 /sbin/init
		  bash        1313   user  cwd    DIR  259,1     4096  641 /home/user
		  bash        1313   user  txt    REG  259,1  1113504 1496 /bin/bash
		  sshd        2586   root  cwd    DIR  259,1     4096    2 /
		  sshd        2586   root  txt    REG  259,1   941960 1453 /usr/sbin/sshd
		  sshd        3014   user  cwd    DIR  259,1     4096  641 /home/user
		  sshd        3014   user  txt    REG  259,1   941960 1453 /usr/sbin/sshd
		  ```
	- **`df -hT`** - Command to display disk space usage and filesystem types.
		- **Options**:
			- `-h`: Display information in a human-readable format.
			- `-T`: Show the filesystem type.
		- **Example**:
			- Command: `df -hT`
			- Output:
			  ```plaintext
			  Filesystem     Type      Size  Used Avail Use% Mounted on
			  udev           devtmpfs  3.9G     0  3.9G   0% /dev
			  tmpfs          tmpfs     798M  1.3M  797M   1% /run
			  /dev/sda1      ext4       50G   15G   32G  32% /
			  tmpfs          tmpfs     3.9G  220K  3.9G   1% /dev/shm
			  tmpfs          tmpfs     5.0M     0  5.0M   0% /run/lock
			  tmpfs          tmpfs     798M   44K  798M   1% /run/user/1000
			  ```
- #File_Management
  collapsed:: true
	- **`rm -rf /nmt/junkdirectory/`** - Command to forcefully and recursively remove a directory and its contents.
		- **Options**:
			- `-r` (recursive): Recursively remove directories and their contents.
			- `-f` (force): Force removal without confirmation.
		- **Example**:
			- Command: `rm -rf /nmt/junkdirectory/`
		- **Caution**: This command permanently deletes the directory and its contents without asking for confirmation.
	- **`sshfs`** - A tool to mount remote filesystems over SSH.
		- **Command Example**:
			- Command: `sudo sshfs dev@192.168.200.52:/mnt/backup /mnt/backup_remote -o port=33555,allow_other`
		- **Explanation**:
			- **`sudo`**: Executes the command with superuser privileges.
			- **`sshfs`**: Utility to mount a remote filesystem over SSH.
			- **`dev@192.168.200.52:/mnt/backup`**: Remote directory to be mounted.
				- `dev`: Username on the remote server.
				- `192.168.200.52`: IP address of the remote server.
				- `/mnt/backup`: Directory on the remote server to mount.
			- **`/mnt/backup_remote`**: Local mount point.
			- **`-o`**: Mount options.
				- **`port=33555`**: Specifies that SSH should use port 33555 instead of the default port 22.
				- **`allow_other`**: Allows other users to access the mounted filesystem.
		- **Useful Commands**:
			- **Mounting a remote filesystem**:
				- Command: `sudo sshfs user@remote_host:/remote/directory /local/mountpoint -o options`
			- **Unmounting a remote filesystem**:
				- Command: `sudo fusermount -u /local/mountpoint`
- #Network_Management
  collapsed:: true
	- **`iptables`** - A powerful utility for configuring network packet filtering rules in Linux.
		- **Tables**:
			- **filter**: Main table for packet filtering.
			- **nat**: Table for Network Address Translation (NAT).
			- **mangle**: Table for packet alteration.
			- **raw**: Table for raw packets (used for connection tracking exemptions).
			- **security**: Table for SELinux.
		- **Chains**:
			- **INPUT**: Processes incoming packets destined for the local system.
			- **OUTPUT**: Processes outgoing packets sent by the local system.
			- **FORWARD**: Processes packets passing through the system (routed).
			- **PREROUTING**: Processes packets before routing.
			- **POSTROUTING**: Processes packets after routing.
		- **Targets**:
			- **ACCEPT**: Allows the packet.
			- **DROP**: Drops the packet.
			- **REJECT**: Drops the packet and sends an error message to the sender.
			- **LOG**: Logs the packet.
			- **MASQUERADE**: Performs masquerading (commonly used in NAT).
		- **Basic Commands**:
			- **List current rules**:
				- Command: `sudo iptables -L`
			- **Add a rule**:
				- Command: `sudo iptables -A [CHAIN] -p [PROTOCOL] --dport [PORT] -j [TARGET]`
				- Example: Allow incoming connections on port 22 (SSH):
				  ```sh
				  sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
				  ```
			- **Delete a rule**:
				- Command: `sudo iptables -D [CHAIN] [RULE_NUMBER]`
				- Example: Delete the first rule in the INPUT chain:
				  ```sh
				  sudo iptables -D INPUT 1
				  ```
			- **Save rules**:
				- Command: `sudo sh -c "iptables-save > /etc/iptables/rules.v4"`
			- **Restore rules**:
				- Command: `sudo iptables-restore < /etc/iptables/rules.v4`
		- **Example Rules**:
			- **Allow incoming connections on port 80 (HTTP)**:
				- Command: `sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT`
			- **Block all incoming connections**:
				- Command: `sudo iptables -P INPUT DROP`
			- **Allow outgoing connections**:
				- Command: `sudo iptables -A OUTPUT -j ACCEPT`
			- **Log dropped packets**:
				- Command: `sudo iptables -A INPUT -j LOG --log-prefix "iptables: "`
	- **`nftables`** - A modern packet filtering framework to replace `iptables`, `ip6tables`, `arptables`, and `ebtables`.
		- **Features**:
			- Unified utility for managing network packet filtering.
			- Supports IPv4, IPv6, ARP, and other protocols.
			- Simplified and flexible syntax for rules.
			- Optimized performance and reduced system load.
			- Supports sets for grouping addresses and other objects.
		- **Installation**:
			- Debian-based systems:
				- Command: `sudo apt-get update`
				- Command: `sudo apt-get install nftables`
		- **Basic Commands**:
			- **Start and enable nftables service**:
				- Command: `sudo systemctl start nftables`
				- Command: `sudo systemctl enable nftables`
			- **Create a new table**:
				- Command: `sudo nft add table inet filter`
			- **Create a new chain**:
				- Command: `sudo nft add chain inet filter input { type filter hook input priority 0 \; }`
			- **Add a rule**:
				- Command: `sudo nft add rule inet filter input tcp dport 22 accept`
			- **List current rules**:
				- Command: `sudo nft list ruleset`
			- **Delete a rule**:
				- Command: `sudo nft delete rule inet filter input handle [handle_number]`
		- **Example Setup**:
			- **Create filter table**:
				- Command: `sudo nft add table inet filter`
			- **Create input chain**:
				- Command: `sudo nft add chain inet filter input { type filter hook input priority 0 \; }`
			- **Create forward chain**:
				- Command: `sudo nft add chain inet filter forward { type filter hook forward priority 0 \; }`
			- **Create output chain**:
				- Command: `sudo nft add chain inet filter output { type filter hook output priority 0 \; }`
			- **Add rules**:
				- Allow incoming SSH connections on port 22:
					- Command: `sudo nft add rule inet filter input tcp dport 22 accept`
				- Block all incoming connections by default:
					- Command: `sudo nft add rule inet filter input drop`
				- Allow outgoing connections:
					- Command: `sudo nft add rule inet filter output accept`
				- Log dropped packets:
					- Command: `sudo nft add rule inet filter input drop log prefix "nftables: "`
		- **`nftables` Practical Tips**
			- **Automation with Scripts**
				- Create a script:
					- Command: `sudo nano /etc/nftables.conf`
				- Example script content:
				  ```plaintext
				  #!/usr/sbin/nft -f
				  
				  table inet filter {
				      chain input {
				          type filter hook input priority 0; policy drop;
				  
				          # Allow established and related connections
				          ct state established,related accept
				  
				          # Allow loopback traffic
				          iif lo accept
				  
				          # Allow SSH
				          tcp dport 22 accept
				  
				          # Allow HTTP and HTTPS
				          tcp dport { 80, 443 } accept
				  
				          # Log and drop other traffic
				          log prefix "nftables: input drop: " flags all counter
				          drop
				      }
				  
				      chain forward {
				          type filter hook forward priority 0; policy drop;
				      }
				  
				      chain output {
				          type filter hook output priority 0; policy accept;
				      }
				  }
				  
				  table ip nat {
				      chain prerouting {
				          type nat hook prerouting priority -100; policy accept;
				      }
				  
				      chain postrouting {
				          type nat hook postrouting priority 100; policy accept;
				          ip saddr 192.168.1.0/24 oif eth0 masquerade
				      }
				  }
				  ```
				- Apply the script:
					- Command: `sudo nft -f /etc/nftables.conf`
				- Enable automatic application on boot:
					- Command: `sudo systemctl enable nftables`
			- **Using Sets**
				- Create a set for allowed IP addresses:
					- Command: `sudo nft add set inet filter allowed_ips { type ipv4_addr\; }`
				- Add IP addresses to the set:
					- Command: `sudo nft add element inet filter allowed_ips { 192.168.1.100, 192.168.1.101 }`
				- Use the set in rules:
					- Command: `sudo nft add rule inet filter input ip saddr @allowed_ips accept`
			- **Managing NAT**
				- Create NAT table and chains:
					- Command: `sudo nft add table ip nat`
					- Command: `sudo nft add chain ip nat prerouting { type nat hook prerouting priority -100 \; }`
					- Command: `sudo nft add chain ip nat postrouting { type nat hook postrouting priority 100 \; }`
				- Add masquerading rule:
					- Command: `sudo nft add rule ip nat postrouting oif eth0 masquerade`
			- **Logging Traffic**
				- Add logging rule for incoming traffic:
					- Command: `sudo nft add rule inet filter input log prefix "nftables: input: " flags all counter`
			- **DDoS Protection**
				- Rate limit incoming SSH traffic:
					- Command: `sudo nft add rule inet filter input tcp dport 22 ct state new limit rate 15/minute accept`
					- Command: `sudo nft add rule inet filter input tcp dport 22 drop`
- #Disk_Management
  collapsed:: true
	- **RAID** - Redundant Array of Independent Disks
		- **Levels**:
			- **RAID 0**: Striping, no redundancy.
			- **RAID 1**: Mirroring, high redundancy.
			- **RAID 5**: Striping with parity, single disk fault tolerance.
			- **RAID 6**: Striping with double parity, dual disk fault tolerance.
			- **RAID 10**: Combination of RAID 1 and RAID 0.
	- **`mdadm`** - Utility for managing software RAID arrays in Linux.
		- **Commands**:
			- Create RAID array:
				- Command: `sudo mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/sda1 /dev/sdb1`
			- Add device to RAID array:
				- Command: `sudo mdadm --manage /dev/md0 --add /dev/sdc1`
			- Remove device from RAID array:
				- Command: `sudo mdadm --manage /dev/md0 --fail /dev/sdb1`
				- Command: `sudo mdadm --manage /dev/md0 --remove /dev/sdb1`
			- View RAID array status:
				- Command: `sudo mdadm --detail /dev/md0`
			- Create configuration file:
				- Command: `sudo mdadm --detail --scan | sudo tee -a /etc/mdadm/mdadm.conf`
			- Reassemble RAID array:
				- Command: `sudo mdadm --assemble --scan`
	- **`mkfs`** - Make File System, utility to create a filesystem on a partition or logical volume.
		- **Commands**:
			- Create ext4 filesystem:
				- Command: `sudo mkfs.ext4 /dev/sda1`
			- Create xfs filesystem:
				- Command: `sudo mkfs.xfs /dev/sda1`
			- Create vfat (FAT32) filesystem:
				- Command: `sudo mkfs.vfat /dev/sda1`
	- **`fdisk`** - Utility to create, modify, and delete partitions on hard drives.
		- **Commands**:
			- Start `fdisk` for a device:
				- Command: `sudo fdisk /dev/sda`
			- **Interactive Commands**:
				- **`p`**: Print partition table.
				- **`n`**: Create a new partition.
				- **`d`**: Delete a partition.
				- **`t`**: Change partition type.
				- **`w`**: Write changes to disk and exit.
				- **`q`**: Quit without saving changes.
			- **Example**:
				- Start `fdisk`: `sudo fdisk /dev/sda`
				- Create a new partition: Press `n`, select type, number, start, and end sectors.
				- Write changes: Press `w`.
- #Log_Management
  collapsed:: true
	- **`/etc/logrotate.d/nginx`** - Configuration file for `logrotate` to manage Nginx log files.
		- **Example Content**:
		  ```plaintext
		  /var/log/nginx/*.log {
		      daily
		      missingok
		      rotate 14
		      compress
		      delaycompress
		      notifempty
		      create 0640 www-data adm
		      sharedscripts
		      postrotate
		          if [ -f /var/run/nginx.pid ]; then
		              kill -USR1 `cat /var/run/nginx.pid`
		          fi
		      endscript
		  }
		  ```
		- **Explanation**:
			- **/var/log/nginx/*.log**: Specifies the log files to rotate.
			- **daily**: Rotate logs daily.
			- **missingok**: Ignore missing log files and do not throw an error.
			- **rotate 14**: Keep 14 old log files before deleting them.
			- **compress**: Compress rotated log files using gzip.
			- **delaycompress**: Delay compression until the next rotation.
			- **notifempty**: Do not rotate empty log files.
			- **create 0640 www-data adm**: Create new log files with specified permissions, owner, and group.
			- **sharedscripts**: Ensure postrotate and prerotate scripts run only once.
			- **postrotate...endscript**: Script to execute after rotation. Sends USR1 signal to Nginx to reopen log files.
- #Disk_Performance_Testing
  collapsed:: true
	- **Command**: `sync; dd if=/dev/zero of=tempfile bs=2M count=2048; sync`
		- **Explanation**:
			- `sync`: Writes all cached data to disk, ensuring all file system changes are applied.
			- `dd if=/dev/zero of=tempfile bs=2M count=2048`: Creates a file named `tempfile` filled with zeros, with a total size of 4GB (2048 blocks of 2MB each).
			- `sync`: Ensures all data is written to disk after the file creation.
		- **Purpose**: This command sequence is used for testing disk performance by creating a large file and ensuring all data is written to disk without using the cache.
- #System_Performance_Testing
  collapsed:: true
	- **`sysbench`** - A multi-purpose benchmarking tool for system performance testing.
		- **Features**:
			- CPU performance testing
			- Memory performance testing
			- Disk I/O performance testing
			- Database performance testing (MySQL, PostgreSQL)
		- **Installation**:
			- Debian-based systems:
				- Command: `sudo apt-get update`
				- Command: `sudo apt-get install sysbench`
			- Red Hat-based systems:
				- Command: `sudo yum install sysbench`
		- **Examples**:
			- **CPU testing**:
				- Command: `sysbench --test=cpu --cpu-max-prime=20000 run`
			- **Memory testing**:
				- Command: `sysbench --test=memory --memory-block-size=1M --memory-total-size=10G run`
			- **Disk I/O testing**:
				- Prepare: `sysbench --test=fileio --file-total-size=10G prepare`
				- Run: `sysbench --test=fileio --file-total-size=10G --file-test-mode=rndrw run`
				- Cleanup: `sysbench --test=fileio --file-total-size=10G cleanup`
	- **`stress-ng`** - A tool for stress testing and benchmarking the system.
		- **Features**:
			- CPU stress testing
			- Memory stress testing
			- Disk I/O stress testing
			- Stress testing of various system resources
		- **Installation**:
			- Debian-based systems:
				- Command: `sudo apt-get update`
				- Command: `sudo apt-get install stress-ng`
			- Red Hat-based systems:
				- Command: `sudo yum install epel-release`
				- Command: `sudo yum install stress-ng`
		- **Examples**:
			- **CPU stress testing**:
				- Command: `stress-ng --cpu 4 --timeout 60s`
			- **Memory stress testing**:
				- Command: `stress-ng --vm 2 --vm-bytes 1G --timeout 60s`
			- **Disk I/O stress testing**:
				- Command: `stress-ng --hdd 2 --timeout 60s`
			- **Comprehensive system stress testing**:
				- Command: `stress-ng --cpu 4 --vm 2 --vm-bytes 1G --hdd 2 --timeout 60s`
- #System_Management
  collapsed:: true
	- **Resetting Root Password on Debian**
		- **Step 1: Reboot the Server**
			- Command: `sudo reboot`
			- Alternatively, press the physical reboot button if you have direct access.
		- **Step 2: Access GRUB Bootloader**
			- During the reboot, press `Esc` or `Shift` to access the GRUB menu.
		- **Step 3: Edit Boot Parameters**
			- Select the current kernel entry and press `e` to edit.
			- Add `single` or `init=/bin/bash` to the end of the `linux` line.
				- Example:
				  ```plaintext
				  linux /boot/vmlinuz-4.19.0-16-amd64 root=UUID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx ro quiet single
				  ```
				  or
				  ```plaintext
				  linux /boot/vmlinuz-4.19.0-16-amd64 root=UUID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx ro quiet init=/bin/bash
				  ```
			- Press `Ctrl` + `X` or `F10` to boot with the modified parameters.
		- **Step 4: Change Root Password**
			- If using `init=/bin/bash`, remount the filesystem as read-write:
				- Command: `mount -o remount,rw /`
			- Change the root password:
				- Command: `passwd root`
				- Enter the new password twice.
			- Synchronize and reboot:
				- Command: `sync`
				- Command: `reboot -f`
	- **`systemd-resolved`** - A service for DNS resolution and caching, part of systemd.
		- **Installation**:
			- Command: `sudo apt-get update`
			- Command: `sudo apt-get install systemd-resolved`
		- **Start and enable service**:
			- Command: `sudo systemctl start systemd-resolved`
			- Command: `sudo systemctl enable systemd-resolved`
		- **Configuration**:
			- File: `/etc/systemd/resolved.conf`
			- Example content:
			  ```ini
			  [Resolve]
			  DNS=8.8.8.8 8.8.4.4
			  FallbackDNS=1.1.1.1 1.0.0.1
			  Domains=example.com
			  DNSSEC=yes
			  ```
		- **Symbolic link for /etc/resolv.conf**:
			- Command: `sudo ln -sf /run/systemd/resolve/resolv.conf /etc/resolv.conf`
		- **Check status**:
			- Command: `resolvectl status`
		- **Example usage**:
			- Query DNS: `resolvectl query example.com`
			- Check DNS servers: `resolvectl dns`
			- Set DNS server: `resolvectl dns eth0 8.8.8.8`
	- **`nscd`** - Name Service Cache Daemon, caches results from various name services.
		- **Installation**:
			- Command: `sudo apt-get update`
			- Command: `sudo apt-get install nscd`
		- **Start and enable service**:
			- Command: `sudo systemctl start nscd`
			- Command: `sudo systemctl enable nscd`
		- **Configuration**:
			- File: `/etc/nscd.conf`
			- Example content:
			  ```ini
			  logfile /var/log/nscd.log
			  
			  enable-cache    passwd          yes
			  positive-time-to-live   passwd  600
			  negative-time-to-live   passwd  20
			  
			  enable-cache    group           yes
			  positive-time-to-live   group   3600
			  negative-time-to-live   group   60
			  
			  enable-cache    hosts           yes
			  positive-time-to-live   hosts   3600
			  negative-time-to-live   hosts   20
			  ```
		- **Check status**:
			- Command: `sudo nscd -g`
		- **Example usage**:
			- Invalidate hosts cache: `sudo nscd -i hosts`
			- Invalidate passwd cache: `sudo nscd -i passwd`
- #Archiving_and_Compression
  collapsed:: true
	- **Definitions**:
		- **Archiving**:
			- Combining multiple files into a single file without reducing their size.
			- Common tool: `tar`.
		- **Compression**:
			- Reducing the size of files to save space or for faster transfer.
			- Common tools: `gzip`, `bzip2`, `xz`.
	- **Archiving with `tar`**
		- **Definition**:
			- `tar` (tape archive) is used to create, extract, and manage archive files.
		- **Common Commands**:
			- **Create an archive**:
			  ```bash
			  tar -cvf archive.tar file1 file2 directory/
			  ```
				- `-c`: Create a new archive.
				- `-v`: Verbose mode (show progress).
				- `-f`: Specify the archive file name.
			- **Extract an archive**:
			  ```bash
			  tar -xvf archive.tar
			  ```
				- `-x`: Extract files from an archive.
			- **List contents of an archive**:
			  ```bash
			  tar -tvf archive.tar
			  ```
			- **Add files to an archive**:
			  ```bash
			  tar -rvf archive.tar newfile
			  ```
				- `-r`: Append files to an archive.
		- **Best Practices**:
			- Use meaningful names for archive files, including timestamps (e.g., `backup_2025-01-10.tar`).
	- **Compression Tools**
		- **gzip**:
			- Compress a file:
			  ```bash
			  gzip file.txt
			  ```
				- Creates `file.txt.gz`.
			- Decompress a file:
			  ```bash
			  gunzip file.txt.gz
			  ```
			- Combine with `tar`:
			  ```bash
			  tar -czvf archive.tar.gz file1 file2
			  ```
				- `-z`: Use gzip for compression.
		- **bzip2**:
			- Compress a file:
			  ```bash
			  bzip2 file.txt
			  ```
				- Creates `file.txt.bz2`.
			- Decompress a file:
			  ```bash
			  bunzip2 file.txt.bz2
			  ```
			- Combine with `tar`:
			  ```bash
			  tar -cjvf archive.tar.bz2 file1 file2
			  ```
				- `-j`: Use bzip2 for compression.
		- **xz**:
			- Compress a file:
			  ```bash
			  xz file.txt
			  ```
				- Creates `file.txt.xz`.
			- Decompress a file:
			  ```bash
			  unxz file.txt.xz
			  ```
			- Combine with `tar`:
			  ```bash
			  tar -cJvf archive.tar.xz file1 file2
			  ```
				- `-J`: Use xz for compression.
		- **zip**:
			- Create a compressed archive:
			  ```bash
			  zip archive.zip file1 file2
			  ```
			- Extract a zip archive:
			  ```bash
			  unzip archive.zip
			  ```
	- **Comparing Compression Tools**
		- **gzip**:
			- Fast, moderate compression ratio.
		- **bzip2**:
			- Slower, higher compression ratio than gzip.
		- **xz**:
			- Slowest, highest compression ratio.
		- **zip**:
			- Compresses and archives in one step; widely compatible.
	- **Practical Examples**
		- **Create a backup archive**:
		  ```bash
		  tar -czvf /backup/backup_$(date +%F).tar.gz /home/user/
		  ```
			- Compresses the `/home/user/` directory into a gzip archive with the current date.
		- **Extract specific files from an archive**:
		  ```bash
		  tar -xvf archive.tar file1 file2
		  ```
		- **Compress large log files**:
		  ```bash
		  gzip -9 large_log_file.log
		  ```
			- `-9`: Maximum compression level.
		- **Extract and decompress in one step**:
		  ```bash
		  tar -xzvf archive.tar.gz
		  ```
	- **Best Practices**
		- Use `tar` with compression flags (`-z`, `-j`, `-J`) for simplicity.
		- Automate backups with scripts and include compression to save space.
		- Use `zip` for cross-platform compatibility.
- #ssh
  collapsed:: true
	- **Definition**:
		- SSH is a protocol for securely accessing and managing remote servers.
		- Provides encrypted communication between the client and the server.
	- **Basic Commands**:
		- **Connect to a remote server**:
		  ```bash
		  ssh username@remote_host
		  ```
		- **Specify a custom port**:
		  ```bash
		  ssh -p 2222 username@remote_host
		  ```
		- **Run a single command on a remote server**:
		  ```bash
		  ssh username@remote_host 'ls -la /path/to/directory'
		  ```
		- **Key-Based Authentication**:
			- Generate an SSH key pair:
			  ```bash
			  ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
			  ```
			- Copy the public key to the remote server:
			  ```bash
			  ssh-copy-id username@remote_host
			  ```
			- Connect to the server without a password:
			  ```bash
			  ssh username@remote_host
			  ```
- #scp
  collapsed:: true
	- **Definition**:
		- A command-line utility for securely transferring files between a local and a remote system (or between two remote systems) over SSH.
	- **Basic Commands**:
		- **Copy a file from local to remote**:
		  ```bash
		  scp file.txt username@remote_host:/path/to/destination
		  ```
		- **Copy a file from remote to local**:
		  ```bash
		  scp username@remote_host:/path/to/file.txt /local/destination
		  ```
		- **Copy a directory recursively**:
		  ```bash
		  scp -r /local/directory username@remote_host:/remote/destination
		  ```
		- **Use a custom SSH port**:
		  ```bash
		  scp -P 2222 file.txt username@remote_host:/path/to/destination
		  ```