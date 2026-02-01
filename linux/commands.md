# Linux Commands - **Commands in Linux (with purpose)**

## `ls`

List directory contents

```md
ls
ls -l
ls -la
```

---

## `cd`

Change directory

```md
cd /path/to/dir
cd ..
cd ~
```

---

## `pwd`

Show current directory path

```md
pwd
```

---

## `cp`

Copy files/directories

```md
cp file1.txt file2.txt
cp -r dir1/ dir2/
```

---

## `mv`

Move or rename files

```md
mv old.txt new.txt
mv file.txt /path/to/dir/
```

---

## `rm`

Delete files/directories

```md
rm file.txt
rm -r dir/
rm -rf dir/
```

---

## `mkdir`

Create directory

```md
mkdir newdir
mkdir -p path/to/newdir
```

---

## `rmdir`

Delete empty directory

```md
rmdir emptydir
```

---

## `touch`

Create empty file / update timestamp

```md
touch newfile.txt
```

---

## `cat`

Display file content

```md
cat file.txt
cat file1.txt file2.txt
```

---

## `--help`

Purpose: Quick usage summary

```md
ls --help
grep --help
```

Test:

```md
df --help
```

---

## `man`

Purpose: Full manual

```md
man ls
```

Search inside man:

```md
/size
```

Quit:

```md
q
```

---

## `echo`

```md
echo hello
```

Append to file:

```md
echo hello >> file.txt
```

Test:

```md
cat file.txt
```

---

## `date`

```md
date
```

Save timestamp:

```md
date >> file.txt
```

---

## `less`

Scrollable viewer

```md
less file.txt
```

---

## `more`

```md
more file.txt
```

---

## `head`

```md
head file.txt
head -n 5 file.txt
```

---

## `tail`

```md
tail file.txt
tail -n 5 file.txt
```

Live follow:

```md
tail -f file.txt
```

---

## `zcat`

View compressed file:

```md
zcat file.txt.gz
```

Test:

```md
gzip file.txt
zcat file.txt.gz
```

---

## `wc`

```md
wc file.txt
wc -l file.txt
```

---

## `cut`

```md
cut -d ":" -f1 /etc/passwd
```

---

## `find`

Find files by name:

```md
find . -name "file.txt"
```

---

## `grep`

Search inside files:

```md
grep "hello" file.txt
grep -r "hello" .
```

---

## `tee`

Write + display:

```md
echo hello | tee out.txt
```

Append:

```md
echo hi | tee -a out.txt
```

---

## `sort`

```md
sort file.txt
sort -n numbers.txt
```

---

## `diff`

Compare files:

```md
diff file1.txt file2.txt
```

---

## `df`

Disk free:

```md
df
df -h
```

---

## `du`

Directory size:

```md
du .
du -sh .
```

---

## `free`

Memory:

```md
free
free -h
```

---

## `vmstat`

Memory + CPU:

```md
vmstat
vmstat 1
```

---

## `top`

```md
top
```

Quit:

```md
q
```

---

## `htop`

```md
htop
```

if missing -

```md
sudo apt install htop
```

---

## `ps`

```md
ps
ps aux
```

---

## `sudo -i`

Root shell:

```md
sudo -i
```

---

## `sudo su -`

```md
sudo su -
```

Exit root:

```md
exit
```

---

## `fuser`

Find process using file:

```md
fuser file.txt
```

Find who uses port 80:

```md
fuser -n tcp 80
```

Kill:

```md
fuser -k 80/tcp
```

---

## Connect

```md
ssh user@server_ip
```

---

## With key

```md
ssh -i key.pem user@server_ip
```

---

## Exit

```md
exit
```

---
