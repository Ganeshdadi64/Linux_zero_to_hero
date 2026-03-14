# Linux Interview Questions and Answers

---

# 1️⃣ Why `/etc/passwd` and `/etc/shadow` cannot be merged into one file?

## What the Interviewer Is Asking

The interviewer wants to check your **Linux security knowledge**.

👉 Why does Linux store **user information** and **password hashes** in two different files?

---

## Explanation

Linux separates user information into two files for **security reasons**.

| File | Purpose | Access |
|---|---|---|
| `/etc/passwd` | Stores user account information | Readable by all users |
| `/etc/shadow` | Stores encrypted passwords | Accessible only by root |

If both were merged into one file:

- Any user could see password hashes
- Attackers could attempt **offline password cracking**

So Linux separates them to improve **system security**.

---

## Example `/etc/passwd`

```
john:x:1001:1001:John:/home/john:/bin/bash
```

Fields:

```
username : password placeholder : UID : GID : comment : home directory : shell
```

Note:

```
x
```

means the **password is stored in `/etc/shadow`**.

---

## Example `/etc/shadow`

```
john:$6$abcd1234$hashedpassword:19000:0:99999:7:::
```

This file stores **encrypted passwords**.

Only **root** can read it.

---

## Quick Interview Answer

Linux separates `/etc/passwd` and `/etc/shadow` to improve security. The `/etc/passwd` file stores user account information and is readable by all users, while `/etc/shadow` stores encrypted passwords and is accessible only to the root user. This separation prevents unauthorized users from accessing password hashes and improves system security.

---
# 2️⃣ How to list all the files opened by a particular PID?

## Explanation

In Linux, every running process may open multiple files such as:

- log files
- configuration files
- sockets
- libraries

We can check which files are opened by a specific process.

---

## Command

```
lsof -p <PID>
```

Example:

```
lsof -p 1234
```

This shows all files opened by process **PID 1234**.

---

## Alternative Method

```
ls -l /proc/<PID>/fd
```

Example:

```
ls -l /proc/1234/fd
```

This displays file descriptors used by the process.

---

## Quick Interview Answer

To list all files opened by a particular process in Linux, we use the `lsof -p <PID>` command. This command displays all files currently opened by the specified process.

---

# 3️⃣ We are unable to unmount the filesystem. What could be the reason?

## Explanation

A filesystem cannot be unmounted if it is **currently in use**.

Possible reasons:

| Reason | Description |
|---|---|
| Open files | Some process is using files in that filesystem |
| Active directory | A user is currently inside that directory |
| Running application | An application is accessing the filesystem |
| NFS mounts | Network filesystem dependency |

---

## How to Identify the Problem

Check processes using the filesystem.

```
lsof /mountpoint
```

or

```
fuser -m /mountpoint
```

Example:

```
fuser -m /data
```

This shows processes using `/data`.

---

## Solution

Kill the processes:

```
kill -9 <PID>
```

Then unmount:

```
umount /data
```

---

## Quick Interview Answer

A filesystem cannot be unmounted if it is currently being used by a process or if a user is inside that directory. We can identify such processes using commands like `lsof` or `fuser`, terminate them if required, and then unmount the filesystem.

---

# 4️⃣ Why does a server take more time after reboot?

## Explanation

A slow reboot may occur due to several reasons.

Common causes:

| Cause | Description |
|---|---|
| Filesystem check | `fsck` running after improper shutdown |
| Network delays | Waiting for network services |
| Failed services | Some services failing to start |
| Mount issues | Network storage mounts (NFS) |
| Hardware problems | Disk or memory issues |

---

## How to Diagnose

Check boot logs:

```
journalctl -b
```

Check service startup times:

```
systemd-analyze blame
```

---

## Quick Interview Answer

A server may take longer to reboot due to filesystem checks, slow service startup, network delays, or hardware issues. We can analyze boot logs using `journalctl -b` and check service startup times using `systemd-analyze blame`.

---

# 5️⃣ Getting “Permission Denied” while creating a file in a partition

## Possible Reasons

| Reason | Explanation |
|---|---|
| No write permission | User does not have write access |
| Filesystem mounted as read-only | Partition mounted with `ro` option |
| Disk full | No space left on device |
| File system errors | Partition corruption |

---

## Check Permissions

```
ls -ld /directory
```

---

## Check Disk Space

```
df -h
```

---

## Check Mount Mode

```
mount | grep /partition
```

If mounted read-only:

```
mount -o remount,rw /partition
```

---

## Quick Interview Answer

“Permission denied” while creating a file may occur if the user lacks write permissions, the filesystem is mounted as read-only, or the disk is full. We can check permissions using `ls -ld`, disk space using `df -h`, and mount options using the `mount` command.

---

# 6️⃣ How to check kernel routing table in Linux?

## Explanation

The kernel routing table determines how network packets are routed.

---

## Commands

### Modern Command

```
ip route
```

Example:

```
ip route show
```

---

### Older Command

```
route -n
```

---

## Example Output

```
default via 192.168.1.1 dev eth0
192.168.1.0/24 dev eth0 proto kernel scope link
```

This shows:

- Default gateway
- Network routes
- Network interface

---

## Quick Interview Answer

We can check the Linux kernel routing table using the `ip route` command. It displays the routing information used by the system to forward network packets.

---

# 7️⃣ How to set Sticky Bit in Linux?

## Explanation

Sticky bit is used mainly on **shared directories**.

It ensures:

👉 Only the **file owner** or **root** can delete files.

Even if other users have write permission.

---

## Example

Directory like `/tmp` uses sticky bit.

Check:

```
ls -ld /tmp
```

Output:

```
drwxrwxrwt
```

`t` indicates sticky bit.

---

## Set Sticky Bit

```
chmod +t directory
```

Example:

```
chmod +t /shared
```

---

## Remove Sticky Bit

```
chmod -t directory
```

---

## Quick Interview Answer

The sticky bit is used on shared directories to ensure that only the file owner or root user can delete files. It can be set using the `chmod +t` command.

---

# 8️⃣ Difference between small **s** and capital **S** in Linux permissions

## Explanation

`s` and `S` are related to **SetUID and SetGID permissions**.

---

## Small `s`

Example:

```
-rwsr-xr-x
```

Meaning:

- SetUID is enabled
- Execute permission is also enabled

So the program runs with **owner privileges**.

Example command:

```
chmod u+s file
```

---

## Capital `S`

Example:

```
-rwSr-xr-x
```

Meaning:

- SetUID is enabled
- Execute permission is **NOT enabled**

So the permission is **incorrectly configured**.

---

## Summary Table

| Symbol | Meaning |
|---|---|
| `s` | SetUID/SetGID with execute permission |
| `S` | SetUID/SetGID without execute permission |

---

## Quick Interview Answer

In Linux permissions, lowercase **s** indicates that SetUID or SetGID is enabled along with execute permission. Uppercase **S** means SetUID or SetGID is enabled but execute permission is not set, which usually indicates incorrect permission configuration.
