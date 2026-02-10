# 💽 Linux Volume Management & AWS EBS Notes 📘🚀

==================================================

☁️ AWS EBS (Elastic Block Store)
--------------------------------------------------

EBS is a block storage service used with EC2.

Uses:
• Store OS data 💻
• Store application data 📁
• Backup using snapshots 📦

Types:
• gp3 → General Purpose
• io1/io2 → High IOPS
• st1 → Throughput
• sc1 → Cold HDD

--------------------------------------------------

🛠️ Create & Attach EBS to EC2
--------------------------------------------------

Steps (AWS Console):

1. Go to EC2 Dashboard
2. Click Volumes → Create Volume
3. Select size & AZ
4. Click Create
5. Select volume → Attach
6. Choose EC2 instance

After attaching → Configure in Linux

--------------------------------------------------

📋 lsblk Command
--------------------------------------------------

Shows all disks and partitions.

Command:
lsblk

Example Output:
xvda    8:0    0   20G  0 disk
└─xvda1 8:1    0   20G  0 part /

xvdf    8:80   0   10G  0 disk

Meaning:
• xvda → Root disk
• xvdf → Extra attached disk

--------------------------------------------------

📌 What is xvda?
--------------------------------------------------

xvda = Main/root disk in EC2

Contains:
• OS
• Boot files
• Root filesystem

Usually mounted on: /

--------------------------------------------------

📊 df Command
--------------------------------------------------

Shows disk space usage.

Command:
df -h

Example:
Filesystem   Size  Used  Avail
/dev/xvda1    20G   5G    15G

--------------------------------------------------

🖥️ What is VM (Virtual Machine)?
--------------------------------------------------

VM is a virtual computer created inside a physical system.

Features:
• Has own OS 🐧
• Own RAM & CPU
• Isolated environment

Examples:
• EC2
• VirtualBox
• VMware

--------------------------------------------------

📦 LVM (Logical Volume Manager)
--------------------------------------------------

LVM manages disk space flexibly.

Benefits:
• Resize easily 📈
• Combine disks
• Better storage management

--------------------------------------------------

🧱 LVM Components
--------------------------------------------------

1️⃣ Physical Volume (PV)
→ Actual disk
→ Example: /dev/xvdf

2️⃣ Volume Group (VG)
→ Group of PVs
→ Storage pool

3️⃣ Logical Volume (LV)
→ Virtual partition
→ Used by OS

Structure:
Disk → PV → VG → LV → FileSystem

--------------------------------------------------

📌 Check LVM Info
--------------------------------------------------

pvs   → Show physical volumes
vgs   → Show volume groups
lvs   → Show logical volumes

--------------------------------------------------

🛠️ LVM Creation Process (Step by Step)
--------------------------------------------------

Assume new disk: /dev/xvdf

Step 1: Create Physical Volume
pvcreate /dev/xvdf

Step 2: Create Volume Group
vgcreate myvg /dev/xvdf

Step 3: Create Logical Volume
lvcreate -L 5G -n mylv myvg

Step 4: Format LV
mkfs.ext4 /dev/myvg/mylv

Step 5: Create Mount Directory
mkdir /data

Step 6: Mount LV
mount /dev/myvg/mylv /data

--------------------------------------------------

📁 Mounting (Temporary Mount)
--------------------------------------------------

Manual mount:
mount /dev/xvdf /mnt

Unmount:
umount /mnt

After reboot → mount removed ❌

--------------------------------------------------

📁 Permanent Mount (/etc/fstab)
--------------------------------------------------

Edit file:
nano /etc/fstab

Add entry:
UUID=xxxx  /data  ext4  defaults  0 0

Apply:
mount -a

--------------------------------------------------

📌 Mount Using Logical Volume
--------------------------------------------------

Example:
mount /dev/myvg/mylv /data

Check:
df -h

--------------------------------------------------

📌 Mount Direct Disk (Without LVM)
--------------------------------------------------

Step 1: Format disk
mkfs.ext4 /dev/xvdf

Step 2: Create folder
mkdir /disk

Step 3: Mount
mount /dev/xvdf /disk

--------------------------------------------------

📈 Resize EBS Volume (AWS + Linux)
--------------------------------------------------

Step 1: Increase volume in AWS Console

Step 2: Rescan disk
lsblk

Step 3: Resize Partition (if needed)
growpart /dev/xvda 1

Step 4: Resize File System
resize2fs /dev/xvda1

--------------------------------------------------

📈 Resize LVM Volume
--------------------------------------------------

Step 1: Extend LV
lvextend -L +5G /dev/myvg/mylv

OR use full space:
lvextend -l +100%FREE /dev/myvg/mylv

Step 2: Resize filesystem
resize2fs /dev/myvg/mylv

--------------------------------------------------

📌 Add New Disk to Existing LVM
--------------------------------------------------

New disk: /dev/xvdg

Step 1:
pvcreate /dev/xvdg

Step 2:
vgextend myvg /dev/xvdg

Step 3:
lvextend -l +100%FREE /dev/myvg/mylv

Step 4:
resize2fs /dev/myvg/mylv

--------------------------------------------------

🔍 Check Disk & Mount Info
--------------------------------------------------

lsblk
df -h
mount
blkid

--------------------------------------------------

🧠 LVM vs Normal Partition
--------------------------------------------------

Normal Partition:
❌ Hard to resize
❌ Fixed size

LVM:
✅ Easy resize
✅ Flexible
✅ Combine disks

--------------------------------------------------

📌 Common Disk Commands
--------------------------------------------------

fdisk -l       → Show disks
lsblk          → List block devices
blkid          → Show UUID
mount          → Show mounts
df -h          → Space usage
du -sh *        → Folder size

--------------------------------------------------

🧠 SUMMARY
--------------------------------------------------

✔ EBS = Cloud disk storage  
✔ lsblk = Shows disks  
✔ xvda = Root disk  
✔ df = Disk usage  
✔ VM = Virtual Machine  
✔ LVM = Flexible disk manager  
✔ PV → VG → LV = LVM structure  
✔ Mount = Attach storage  
✔ Resize = Increase disk size  

==================================================
🎉 END OF VOLUME MANAGEMENT NOTES 🚀

