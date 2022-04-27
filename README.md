# Build an NVIDIA GPU Accelerated Supercomputer using Jetson development kit 
this is instruction of how to set up a cluster with Jetson nano development kit
we will need at least two Jetson board. you will need Ethernet, hub or switch, a monitor, keyboard, and mouse. 
# Configuration 
make sure you use the same user account name a cross all the board and each board should be in the same physical network  with the same subnet.
Install the prerequisite software on every node:
```
sudo apt-get update
sudo apt-get upgrade 
```
```
sudo apt-get -y install openssh-server git htop python3-pip python-pip nano mpich mpi-default-dev
```
## Setting hostname: 
my cluster will be using the following pattern <Master, node1, node2>. 
So, when choosing hostnames for your board, it’s helpful to pick a system and number them in order.
```
sudo vim /etc/hostname
```
for every node in you cluster with Vim editor change <hostname file> and update the hosts file.To make resolution easier, we’re going to add hostnames of the nodes and their IP addresses to the 
/etc/hosts file. Edit /etc/hosts and add the following lines:
```
192.168.1.101 node1
192.168.1.103 node2
192.168.1.104 node3
```
## Reboot 
```
sudo reboot 
```
# Generate SSH Key 
```
ssh–keygen –t rsa 
```
use ssh-keygen command to generate public authentication key pair. Authentication key allow us to connect to any node without applying password.
key mast be generated for each node separately 
```
ssh-copy-id <username@ipadress>
```
or 
```
ssh-copy-id hostname 
```
# NFS - How to set up NFS-Server and NFS-Client

Network file System is a protocol that allow us to share Directories and files on other Devices (Nodes) over the network.
shared directories and files are typically created on file server, running NFS-server.
NFS file is mounted on client node(device), making it available just like folder user created locally.

# Install NFS Server
```
sudo systemctl restart nfs-kernel-server 
```
create a root directory of NFS shares, this is also known as export folder.
set permission so that any user on the client node can access the folder 
```
sudo mkdir /var/nfs/public
sudo chmod 777 /var/nfs/public
```
to define access for NFS client in export file. to grant access NFS client , we will need to edit **/etc/exports**  file 
in a text editor, add one the fallowing. 

```
/var/nfs/public {clientIP} (rw,sync,no_subtree_check)
```
```
sudo systemctl restart nfs-kernel-server 
```
# install NFS on Client
in order to mount the NFS shared file you need to install nfs-common  
```
sudo apt-get install nfs-common 
```
create a local directory this will the mount point for the NFS shares. in my example is used 
```
sudo mkdir /mnt/nfs-public
```
Mount the file share by running the mount command, as follows. There is no output if the command is successful.
```
sudo mount -t nfs {master IP } : /var/nfs/public   /mnt/nfs-public
```
Edit the /etc/fstab file using any text editor.
Add a line defining the NFS share. Insert a tab character between each parameter. It should appear as one line with no line breaks.
```
{IP of NFS server}:{folder path on server} /var/locally-mounted nfs defaults 0 0
```

```
mount {IP of NFS server}:{folder path on server}
```
