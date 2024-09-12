# Configuring-Local-Storage

# Tier 3 Task: Adding a New Virtual Hard Disk and Configuring Local Storage

Description:

Add a new virtual hard disk to a CentOS virtual machine (VM) and configure local storage to optimize disk usage, enhance performance, and ensure data reliability. This task involves virtually adding the new hard disk, partitioning it, creating file systems, and implementing advanced storage features such as LVM (Logical Volume Manager) for efficient storage management within the virtualized environment.

Detailed Guide: Adding a New Virtual Hard Disk and Configuring Local Storage on CentOS 8 VM

# 1. Access the Virtualization Platform

Access the virtualization platform where your CentOS 8 VM is hosted (e.g., VMware, VirtualBox). This will typically be done through the platform’s management console.

# 2. Add a New Virtual Hard Disk

VirtualBox:

Shut down the VM.

Select the VM and click Settings.

Go to the Storage section.

Click the Add Hard Disk button under the Storage Tree and follow the wizard to create a new virtual disk.

<img width="578" alt="vmw" src="https://github.com/user-attachments/assets/8fda9ffd-00c3-4a84-b7b9-5aafd203e795">

40.00GB is a decent disk size:


<img width="576" alt="vmvs" src="https://github.com/user-attachments/assets/31a46fee-63de-4305-bb0a-9bb3bfce4d74">


Start the VM.

# 3. Verify Disk Detection

Once the VM is powered on, log in to CentOS 8 and verify that the new disk is detected.

lsblk

You should see a new disk listed, such as /dev/sdc.

<img width="259" alt="dev" src="https://github.com/user-attachments/assets/68e3dcf9-d68f-4b71-8265-f7c07724fdfd">

# 4. Partition the New Virtual Hard Disk

Use the fdisk or parted command to partition the new disk. Below is an example using fdisk:

sudo fdisk /dev/sdc

Within the fdisk prompt:

Press n to create a new partition.
Choose p for a primary partition.
Choose partition number (usually 1 for the first partition).
Press Enter to accept default first and last sectors.
Press w to write changes and exit.

<img width="458" alt="sdb" src="https://github.com/user-attachments/assets/79af6e42-a92f-49be-952c-94bf8d506c5a">


# 5. Create File Systems on the Partitions

Format the new partition with a file system (e.g., ext4):

sudo mkfs.ext4 /dev/sdc1

<img width="456" alt="sdc" src="https://github.com/user-attachments/assets/18af457e-f297-42d2-ab1c-922dbff2d30f">


# 6. Implement LVM

6.1 Install LVM tools (if not already installed):

sudo dnf install lvm2

6.2 Create Physical Volume (PV):

sudo pvcreate /dev/sdc1

<img width="459" alt="warn" src="https://github.com/user-attachments/assets/b3cb3c09-3ebf-4781-9732-2a98d1f61ea9">


6.3 Create a Volume Group (VG):

sudo vgcreate my_volume_group /dev/sdc1

6.4 Create a Logical Volume (LV):

sudo lvcreate -n my_logical_volume -l 100%FREE my_volume_group

<img width="457" alt="lv" src="https://github.com/user-attachments/assets/cca40c3c-6745-445e-995d-76de66fdfee3">


6.5 Create a File System on the Logical Volume:

sudo mkfs.ext4 /dev/my_volume_group/my_logical_volume

<img width="454" alt="fs" src="https://github.com/user-attachments/assets/653dc550-000b-4cf6-a721-7fbc73f69c42">


# 7. Optimize Storage Performance Settings

Consider the following optimizations for your VM’s storage:

Adjust Disk Cache Settings: In VMware, for example, ensure that the disk caching mode is set appropriately for your workload.

Enable Disk I/O Limits: If your virtualization platform supports it, 
configure I/O limits to prevent a single VM from consuming all disk resources.

Optimize VM Disk Configuration: Use appropriate disk types (e.g., SCSI instead of IDE) and ensure that you are using thin provisioning if applicable.

# 8. Monitor Disk Usage and Performance

You can use various commands and tools to monitor disk usage and performance:

lsblk -l

<img width="423" alt="blk" src="https://github.com/user-attachments/assets/124844bb-4e32-446f-b2b2-fbd021b64f34">


Monitor I/O performance:

iostat -x 1

<img width="450" alt="cdvv" src="https://github.com/user-attachments/assets/9988438c-c511-45d1-8fa3-55a16af27f35">


Check LVM status:

sudo lvs
sudo vgs
sudo pvs

<img width="453" alt="lvss" src="https://github.com/user-attachments/assets/34d8baee-b88d-4601-8ef9-7651ddb07abe">

# 9. Test the Configured Storage Setup
Ensure that the new storage is working as expected:

Mount the Logical Volume:

sudo mkdir /mnt/my_mount_point
sudo mount /dev/my_volume_group/my_logical_volume /mnt/my_mount_point

Verify the mount:

df -h /mnt/my_mount_point

<img width="457" alt="dhf" src="https://github.com/user-attachments/assets/044eb412-4429-4630-806f-857f767e1de1">


Create and test files:

sudo touch /mnt/my_mount_point/test_file
sudo ls -l /mnt/my_mount_point

<img width="387" alt="sudo" src="https://github.com/user-attachments/assets/c35bd719-2d4d-4669-8583-3b557ccb0adf">

By following these steps, you will have successfully added and configured a new virtual hard disk on your CentOS 8 VM, implemented LVM for efficient storage management, and optimized the storage setup for better performance.
