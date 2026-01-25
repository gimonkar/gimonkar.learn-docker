# SSH Private instance through public instance

## 1] first way:
 copy or create the kay-pair file on to the public ec2 server and then ssh into your private ec2 instance in this way you will be able to ssh but copying the key-paire is not the secure way.



## 2] Second Way:
## Prerequisites (quick check)
- You’re using **Windows 10 / 11**
- You connect using **PowerShell or Windows Terminal**
- You already SSH to the **public EC2** successfully
- Both EC2s use the **same key pair**
---

## Step 1: Enable & start SSH Agent on Windows
Open **PowerShell as Administrator** and run:

```powershell
Get-Service ssh-agent
```
If it’s stopped, enable and start it:

```powershell
Set-Service ssh-agent -StartupType Automatic
Start-Service ssh-agent
```
Confirm it’s running:

```powershell
Get-Service ssh-agent
```
State should be **Running** ✅

---

## Step 2: Add your EC2 key to the agent
Still in PowerShell (can be normal user now):

```powershell
ssh-add C:\path\to\your-key.pem
```
If successful, you’ll see something like:

```
Identity added
```
Verify:

```powershell
ssh-add -l
```
---

## Step 3: SSH into the **public EC2** with agent forwarding
⚠️ `**-A**`** is critical**

```powershell
ssh -A ec2-user@<PUBLIC_EC2_PUBLIC_IP>
```
If this works, you are now logged into the public EC2 **with your agent forwarded**.

---

## Step 4: From public EC2 → SSH into private EC2
Use **private IP** of the private instance:

```bash
ssh ec2-user@<PRIVATE_EC2_PRIVATE_IP>
```
🎉 You’re now inside the **private EC2**
 ✅ No key copied
 ✅ Secure
 ✅ Best practice



![image.png](https://eraser.imgix.net/workspaces/17KrzmDpKoJib8iTq1nl/N2lSj4izkAhshqbeBgwRahIEQAg1/image_EDM4uzhlwrwiItVcKuNwj.png?ixlib=js-3.8.0 "image.png")



Note: once you have added the agent it will be there in your system. you can then login from any terminal using the ssh command with -A.





Removing key from agent after work done:

## 1️⃣ Remove **all** keys from the SSH agent
This is the fastest and safest option if you’re done:

```powershell
ssh-add -D
```
You should see:

```
All identities removed.
```
Verify:

```powershell
ssh-add -l
```
Output should be:

```
The agent has no identities.
```
---

## 2️⃣ Remove **a specific key** only
First, list keys currently loaded:

```powershell
ssh-add -l
```
Then remove one key by path:

```powershell
ssh-add -d C:\path\to\your-key.pem
```
---

## 3️⃣ Stop the SSH agent completely (optional)
If you don’t want the agent running at all:

```powershell
Stop-Service ssh-agent
```
Disable auto-start (optional):

```powershell
Set-Service ssh-agent -StartupType Disabled
```


