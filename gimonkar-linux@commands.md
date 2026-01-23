# Linux commands

# Linux Commands — From Basics to Advanced 🐧
## PART 1: Basics (Must-Know)
### 1️⃣ Check where you are
```bash
pwd
```
📌 Shows current directory

---

### 2️⃣ List files and folders
```bash
ls
ls -l
ls -a
ls -lh
```
| Option | Meaning |
| ----- | ----- |
|  | Long format |
|  | Show hidden files |
|  | Human-readable size |
---

### 3️⃣ Change directory
```bash
cd /home/ec2-user
cd ..
cd ~
```
---

### 4️⃣ Create files & folders
```bash
mkdir test
touch file.txt
mkdir -p a/b/c
```
---

### 5️⃣ Delete files & folders ⚠️
```bash
rm file.txt
rm -r folder
rm -rf folder
```
⚠️ **No recycle bin in Linux**

---

## PART 2: Viewing & Editing Files
### 6️⃣ View file content
```bash
cat file.txt
less file.txt
more file.txt
```
Best practice:

- Small file → `cat` 
- Big file → `less` 
---

### 7️⃣ View first / last lines
```bash
head file.txt
tail file.txt
tail -f logfile.log
```
📌 `tail -f` = live logs (VERY IMPORTANT)

---

### 8️⃣ Edit files
```bash
vi file.txt
nano file.txt
```
Basic `vi` usage:

- `i`  → insert
- `Esc`  → command mode
- `:wq`  → save & quit
- `:q!`  → quit without saving
---

## PART 3: File Permissions & Ownership 🔐
### 9️⃣ Check permissions
```bash
ls -l
```
Example:

```
-rwxr-xr--
```
| Symbol | Meaning |
| ----- | ----- |
| r | read |
| w | write |
| x | execute |
---

### 🔟 Change permissions
```bash
chmod 755 file.sh
chmod +x file.sh
```
---

### 1️⃣1️⃣ Change ownership
```bash
chown user file.txt
chown user:group file.txt
```
---

## PART 4: Searching & Filtering 🔍
### 1️⃣2️⃣ Search text
```bash
grep "error" logfile.log
grep -i "error" logfile.log
grep -r "error" /var/log
```
---

### 1️⃣3️⃣ Find files
```bash
find / -name file.txt
find . -type f
find . -size +100M
```
---

## PART 5: System & Process Commands ⚙️
### 1️⃣4️⃣ Check system usage
```bash
top
htop
free -m
df -h
du -sh folder
```
---

### 1️⃣5️⃣ Process management
```bash
ps -ef
kill PID
kill -9 PID
```
---

### 1️⃣6️⃣ Check OS & uptime
```bash
uname -a
uptime
hostname
```
---

## PART 6: Networking Commands 🌐
### 1️⃣7️⃣ Network check
```bash
ip a
ifconfig
ping google.com
```
---

### 1️⃣8️⃣ Port & connection checks
```bash
netstat -tulnp
ss -tulnp
curl http://example.com
wget http://example.com/file
```
---

### 1️⃣9️⃣ DNS & routes
```bash
nslookup google.com
route -n
```
---

## PART 7: Package Management 📦
### Amazon Linux / RHEL / CentOS
```bash
yum install nginx
yum remove nginx
yum update
```
### Ubuntu / Debian
```bash
apt update
apt install nginx
apt remove nginx
```
---

## PART 8: Compression & Archiving 📁
```bash
tar -cvf backup.tar folder
tar -xvf backup.tar
tar -czvf backup.tar.gz folder
unzip file.zip
```
---

## PART 9: Users & Permissions 👤
```bash
whoami
id
who
adduser user1
passwd user1
```
---

## PART 🔟 Disk & Mounting 💾
```bash
lsblk
mount
df -h
```
---

## PART 11: Environment & Variables
```bash
env
echo $PATH
export MY_VAR=value
```
---

## PART 12: Useful Shortcuts & Tricks 💡
| Shortcut | Meaning |
| ----- | ----- |
|  | Stop command |
|  | Pause |
|  | Command history |
|  | Last command |
|  | Clear screen |
---

## MOST IMPORTANT Commands (Interview ⭐)
```
ls
cd
pwd
grep
find
chmod
chown
ps
top
df
du
netstat / ss
curl
tail -f
```
---

## How to Practice (Recommended)
1️⃣ Launch **EC2 Linux instance**
 2️⃣ SSH into it
 3️⃣ Practice:

```bash
ls
cd
tail -f /var/log/messages
grep error /var/log/messages
```


