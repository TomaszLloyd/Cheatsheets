
### General Dev Cheatsheet

##### common settings for `~/.bash_profile`
```shell
# common aliases where $EDITOR is vim -N

# open sublime text
alias subl="open -a 'Sublime Text'"

alias hide='SetFile -a V'
alias hs='history | grep -i $1'
alias ports='sudo lsof -i -P'

# quickly modify bash_profile
alias profile='$EDITOR ~/.bash_profile && . ~/.bash_profile'

# quick and dirty python server on port 8000
alias py="python -m SimpleHTTPServer"

# list all, long list format, human readable
alias ll='ls -alh'

# edit hosts file
alias hosts='$EDITOR /etc/hosts'

#git specific
alias g="git"
alias ga='git add'
alias gs='git status'
alias gcm="git commit -m"
```
###### useful settings for `~/.vimrc`
```shell
# add spell checking and automatic wrapping at recommended 72 columns
autocmd Filetype gitcommit setlocal spell textwidth=72
```

## Sass
If Gulp/Grunt isn't available and using straight Sass/Compass
```shell
sass --watch --compass -t compressed .
```

## .gitignore

+ for WordPress specific sites `wp-content/uploads/`
+ to ignore local changes, add to this file, same format as .gitignore `.git/info/exclude`

##### Packages
it's better to unpack these files and commit the raw source.
git has its own built in compression methods
```shell
*.7z
*.dmg
*.gz
*.iso
*.jar
*.rar
*.tar
*.zip
```
###### Logs and databases
```
*.log
*.sql
*.sqlite
 ```
###### OS generated files
```
.DS_Store
.DS_Store?
._*
.Spotlight-V100
.Trashes
ehthumbs.db
Thumbs.db
```
###### Compiled source
```
*.com
*.class
*.dll
*.exe
*.o
*.so
```
***

### Sys Admin

+ Bulk rename files `for f in *; do mv "$f" "_prepend_$f"; done` will add "_prepend_" to every file name. Bonus: change the line to `do echo mv` to do a dry run before executing the command.
+ Run last command as sudo `sudo !!`
+ Change to last working directory `cd -`
+ System information `uname -a`
+ How much mem is free? `free -m` or `cat /proc/meminfo`
+ All processes, with params + hierarchy `ps auxww -H`
+ Tail all queries hitting mysql `pt-query-digest --processlist h=localhost --print --no-report --user xxxx --password yyyy`
+ Overview of all disks `df -h`
+ Find files over 100mb `find . -size +100M`
+ Files marked for deletion but not yet deleted `lsof | grep delete`
+ Find files created in last 7 days `find . -mtime -7`
+ Monitor a logfile for IP or anything else `tail -f file.log | grep 192.168.1.1`
+ Location of hosts file `/etc/hosts`
+ Location of vhosts `/etc/apache2/httpd.conf`
+ Vhost docroot for sites enabled `/etc/apache2/sites-enabled/`
+ ssh key location `~/.ssh/id_rsa.pub`
+ reading apache error logs `tail -F /var/log/apache2/error.log`
+ Display headers `curl -LI http://www.google.com`
+ get network configuration overview `ifcongif` & `ip addr`
+ get speed of connection and overview `ethtool eth0`
+ get routing table `netstat -rn`
+ processes listening on tcp socket `netstat -nat | grep LIST`
+ Show all ports listening with process PID `netstat -tlnp`
+ to find running processes `ps`
+ query dns and lookup ip `nslookup example.com`
+ send packets to ip and troubleshoot connectivity problems `ping 192.168.118.1`
+ troubleshoot connection problems, see where packets go `traceroute yahoo.com` if traceroute fails on certain hops but continues going, the individual server may not support icmp protocol, which is used by ping and traceroute
+ Traceroute with stats over time `mtr google.com`
+ Get listing of directory sizes `du -sh *`

##### Using Zgrep
```shell

# example of searching gzipped log files:

find . -iname '*other_vhosts_access*\.gz' | xargs -I{} zgrep 'string' "{}" | less

# show unified diff in log:
git log -p -- file.name

# commit chunks:
git commit -p -- file.name

# git show a commit with unified diff:
git show commit -- file.name

```

##### Firewalls

(still adding)

##### Using Rsync
1. From remote to local, dry run first: 
```shell
rsync -vacn username@remote_host:destination_directory ~/dir1
```
2. If this looks good, then remove -n flag and run again

Bonus: To exclude common cvs folders/files like `.git` and `.svn`, you can use:
```shell
rsync -vanc --cvs-exclude
```

##### Git commands
```shell
# unstash changes
git stash pop

# show all stashed changelists
git stash list

# discard last stashed changeset
git stash drop

# show difference between staged changes and last file version
git diff --staged

# unstage file but preserve it's contents
git reset [file]

# remove file from version control but preserve locally
git rm --cached [file]
```

##### Move from SVN to Git and keep history
``` shell
svn st && svn st -u
# make sure to commit any outstanding work

git svn clone http://svnRepoLocation.com/trunk/ --no-metadata --no-minimize-url --authors-file=authors-file-generic.txt com.gitRepoName.git/

# run both of these as a quick sanity check to make sure number of commits is equal
svn log -q http://svnRepoLocation.com/trunk/ | grep ^r | wc -l
git log -q | grep ^commit | wc -l

cd com.gitRepoName.git/
git remote add origin git@github.com:USERNAME/com.gitRepoName.git # or wherever your repo is going to be
git push -u origin --all
git push -u origin --tags

# add new branches i.e. staging, beta, master
git checkout master && git create-branch staging
```
Then check ownership of project folder.
Create new vhosts for stage and beta branches if applicable
Enable vhosts
Make SVN repo read-only to prevent further changes and have a snapshot

##### Best Practices and Common Pitfalls
Use protocol relative URL's when possible. What this means is not specifying the `http` or `https` and allowing the browser to do it.
```html
<a href="<?php echo '//' . $_server['HTTP_HOST'] . '/something/contact-thank-you' ?>">Click Me</a>
```
EXCEPT in cases when you're passing in a return url, like a Marketo form.
```html
<input type="hidden" name="returnURL" value="<?php echo 'http://' . $_SERVER['HTTP_HOST'] . '/about/contact-thank-you' ?>">
```

##### Regular Expressions
```shell
##  # Characters Meaning
$	# End of a line
^	# Start of a line
[]	# Character class
?	# The preceding item is optional and matched at most once
*	# Zero or more of the preceding item
+	# One or more of the preceding item
{}	# Match some number of the preceding item
|	# Alternation (true if either of the regexes it separates match)
.	# Any one character
\	# Escape (take the following character literally)
```

##### Awk
```shell
awk '{print $1}' <filename>		# prints first column in file

awk '{print $NF}' <filename> 	# prints number of fields in current file

awk '{print NR}' <filename>		# prints row number being processes

awk '{print $1, $2}' <filename> # prints columns 1 and 2 with a space between them
```
Since a `space` is the default delimiter for awk, we can specify something else with the `-F` flag like this `awk -F '-' '{print $1}' file.txt`

You can also use awk to extract a specific row from a file with `NR`.
```shell
awk 'NR==2' file.txt 	# will print only the second row of the file
```

You can return lines with a certain length as well with the `length($0)` command.
```shell
awk 'length($0) > 30' files.txt # prints only lines with more than 30 chars
```

To find the average of numbers in a column we can use the `sum` function
```shell
awk -F '-' '{sum+=$2} END {print "Average age = ",sum/NR}' students.txt
```
In the above example, sum does not have to be declared, but rather it's created on the fly. The `END` function tells awk what to do once the previous command has been ran. In this case, we print the average age of students, or the sum of the second column divided by the number of rows.

In addition to the `END` pattern, awk also has a `BEGIN` pattern which describes an action that needs to be taken before a single line in the file path is processed.
```shell
awk 'BEGIN {print "This is the second line of the file"} NR==2' students.txt

### OUTPUT
This is the second line of the file
Jack Smith - 26 - jack@example.com
```
