# samba

# Samba Server Setup (Linux to Windows File Sharing)

## 📌 Overview

This project demonstrates how to configure a Samba server on Linux
to share a directory with Windows systems over a network.

Samba allows Linux systems to communicate with Windows using the SMB/CIFS protocol.

---

# 🖥 Environment

| Component | Value |
|------------|--------|
| Server OS | CentOS / RHEL |
| Protocol  | SMB/CIFS |
| Shared Directory | /samba/apps |
| Workgroup | SAMBA |
| Access Type | Guest Access |

---

# 🚀 Step 1 – Install Samba Packages

```bash
yum install samba samba-client samba-common -y
```

---

# 📁 Step 2 – Create Shared Directory

```bash
mkdir -p /samba/apps
touch /samba/apps/samba_testfile
```

Set permissions (Lab environment):

```bash
chmod -R 777 /samba
```

---

# 🔐 Step 3 – Configure SELinux Context

Check SELinux context:

```bash
ls -lZ /samba/apps
```

Set Samba context:

```bash
chcon -t samba_share_t /samba/apps
```

Verify:

```bash
ls -lZ /samba/apps
```

---

# ⚙ Step 4 – Configure Samba

Edit configuration file:

```bash
vi /etc/samba/smb.conf
```

Add the following configuration:

```
[global]
workgroup = SAMBA  
netbios name = centos
security = user
map to guest = bad user
dns proxy = no

[Apps]
comment = Shared Dir
path = /samba/apps
browsable = yes
writable = yes
guest ok = yes
guest only = yes
read only = no


```

Check configuration:

```bash
testparm
```

---

# 🔄 Step 5 – Start & Enable Services

```bash
systemctl restart smb nmb
systemctl enable smb nmb
systemctl status smb nmb
```

---

# 🔥 Step 6 – Configure Firewall

```bash
firewall-cmd --permanent --add-service=samba
firewall-cmd --reload
```

Verify IP Address:

```bash
ip a
```

---

# 🖥 Step 7 – Access from Windows

Open Windows File Explorer and type:

```
\\192.168.X.X
```

(Replace with your Linux server IP)

You should see:

```
Apps
```

Open it and verify file access.

---

# 🧪 Testing

From Linux:

```bash
touch /samba/apps/test2
```

Check from Windows — file should appear.

Create a file in Windows inside the Apps folder — verify it appears in:

```bash
ls /samba/apps
```

---

# 🔍 Explanation of Important Parameters

| Parameter | Meaning |
|------------|----------|
| workgroup | Windows workgroup name |
| security = user | User-based authentication |
| map to guest = bad user | Unknown users mapped to guest |
| guest ok = yes | Allow guest access |
| writable = yes | Allow write access |
| browsable = yes | Visible in network |
| read only = no | Enable write permissions |

---

# 🧠 Key Learning Points

- Samba enables Linux-Windows file sharing
- SELinux requires proper context (samba_share_t)
- Firewall must allow samba service
- testparm verifies configuration before restart
- Guest mode allows password-free access (Lab use only)

---

# 🔐 Production Recommendation

For real environments:
- Avoid chmod 777
- Create samba users using:
  ```bash
  smbpasswd -a username
  ```
- Disable guest access
- Use proper SELinux boolean:
  ```bash
  setsebool -P samba_export_all_rw on
  ```

---

# 📚 Commands Used

- yum
- mkdir
- chmod
- chcon
- vi
- testparm
- systemctl
- firewall-cmd
- ip

---

# ✅ Result

Successfully configured Samba server and accessed shared directory from Windows client.

---

## 👨‍💻 Author

sagar sir
Linux System Administration Practice
