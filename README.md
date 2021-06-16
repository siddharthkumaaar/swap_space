# Is your Ubuntu working slow ? Getting hanged many time ?
## Don't worry, Solution is here.
### Increase SWAP space...

#### What is SWAP ?
Swap space in Linux is used when the amount of physical memory (RAM) is full. If the system needs more memory resources and the RAM is full, inactive pages in memory are moved to the swap space. While swap space can help machines with a small amount of RAM, it should not be considered a replacement for more RAM. Swap space is located on hard drives, which have a slower access time than physical memory.

#### Before You Begin...
Generally, the size of the swap file depends on how much RAM your system has:
- Systems with less than 2 GB RAM - 2 times the amount of RAM.
- Systems with 2 to 8 GB RAM - the same size as the amount of RAM.
- Systems with more than 8 GB RAM - at least 4 GB of Swap.

Only root or user with sudo privileges can activate the swap file.

#### Let's move on command side to increase SWAP space...
In this example, we will create 4 GB swap file. If you want to add more swap, replace 4G with the size of the swap space you need.

1. First, create a file that will be used as swap:
```
sudo fallocate -l 4G /swapfile
```
If the `fallocate` utility is not present on your system, or you get an error message saying `fallocate failed: Operation not supported`, use the following command to create the swap file:
```
sudo dd if=/dev/zero of=/swapfile bs=1024 count=4194304
```
2. Set the file permissions to 600 to prevent regular users to write and read the file:
```
sudo chmod 600 /swapfile
```
3. Create a Linux swap area on the file:
```
sudo mkswap /swapfile
```
```
Setting up swapspace version 1, size = 4 GiB (4294967296 bytes)
no label, UUID=ede7d1c5-06ea-500a-9027-fd731d9ab4c9
```
4. Activate the swap file by running the following command:
```
sudo swapon /swapfile
```
To make the change permanent open the /etc/fstab file:
```
sudo nano /etc/fstab
```
and paste the following line:
```
/swapfile swap swap defaults 0 0
```
5. Verify that the swap is active by using either the `swapon` or the `free` command, as shown below:
```
sudo swapon --show
```
```
Output
NAME      TYPE      SIZE  USED PRIO
/swapfile file        4G    0B   -1
```
```
sudo free -h
```
```
Output
              total        used        free      shared  buff/cache   available
Mem:          981Mi        97Mi        68Mi       0.0Ki       814Mi       735Mi
Swap:         4.0Gi        10Mi       3.9Gi
```
### Adjusting the Swappiness Value
Swappiness is a Linux kernel property that defines how often the system will use the swap space. It can have a value between 0 and 100. A low value will make the kernel to try to avoid swapping whenever possible, while a higher value will make the kernel to use the swap space more aggressively.

On Ubuntu, the default swappiness value is set to 60. You can check the current value by typing the following command:

```
cat /proc/sys/vm/swappiness
```
```
Output
60
```
While the swappiness value of `60`is OK for most Linux systems, for production servers, you may need to set a lower value.

For example, to set the swappiness value to `10`, run:
```
sudo sysctl vm.swappiness=10
```
To make this parameter persistent across reboots, append the following line to the `/etc/sysctl.conf file:`
```
vm.swappiness=10
```
The optimal swappiness value depends on your system workload and how the memory is being used. You should adjust this parameter in small increments to find an optimal value.

## Removing a Swap File 
To deactivate and delete the swap file, follow these steps:

1. To deactivate and delete the swap file, follow these steps:
```
sudo swapoff -v /swapfile
```
2. Next, remove the swap file entry `/swapfile swap swap defaults 0 0` from the `/etc/fstab` file.

3.Finally, remove the actual swapfile file using the rm command:
```
sudo rm /swapfile
```
## Conclusion 
We have shown you how to create a swap file and activate and configure swap space on your Ubuntu 20.04 system.

