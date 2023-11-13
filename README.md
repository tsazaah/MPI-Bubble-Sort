# MPI-Bubble-Sort
Executing the Bubble Sort program in parallel using MPI

## Running Bubble Sort Python with MPI Multinode Cluster on Linux Mint

This guide provides step-by-step instructions for creating a master and slave, configuring SSH, configuring NFS, installing MPI, and running Bubble Sort code with Python on Linux Mint.

## Table of Contents
- [Devices and Tools to Prepare](#devices-and-tools-to-prepare)
- [Bridged Topology](#bridged-topology)
- [Creating Master and Slave](#creating-master-and-slave)
- [SSH Configuration](#ssh-configuration)
- [NFS Configuration](#nfs-configuration)
- [MPI Installation](#mpi-installation)
- [Running Python Code - Bubble Sort](#running-python-code---bubble-sort)

## Devices and Tools to Prepare
1. Linux Mint
   - Linux Mint Master
   - Linux Mint Slave 1
   - Linux Mint Slave 2
   - Linux Mint Slave 3
2. MPI (Master and Slave)
3. SSH (Master and Slave)
4. NFS (Master and Slave)
5. Python Bubble Sort Code

## Bridged Topology

![Screenshot 2023-11-13 075701](https://github.com/tsazaah/MPI-Bubble-Sort/assets/150001965/6d06d786-5fb4-42dd-91c5-c838f14e785f)


## Creating Master and Slave
1. Ensure that each master and slave uses a Network Bridge Adapter and is connected to the internet.
2. Determine which device will be the master, slave1, slave2, and slave3.
3. Create a new user with the following command on the master and each slave:
    ```bash
    sudo adduser mpiuser
    ```
4. Grant root access with the command:
    ```bash
    sudo usermod -aG sudo mpiuser
    ```
    Repeat the above steps for each slave.
5. Log in to each server with the user `mpiuser`:
    ```bash
    su - mpiuser
    ```
6. Update Linux Mint and install tools:
    ```bash
    sudo apt update && sudo apt upgrade
    sudo apt install net-tools vim
    ```
7. Configure the `/etc/hosts` file on the master, slave1, slave2, and slave3. Register the IP and hostname of each computer.

## SSH Configuration
1. Install OpenSSH on the master and all slaves:
    ```bash
    sudo apt install openssh-server
    ```
2. Generate a key on the master:
    ```bash
    ssh-keygen -t rsa
    ```
3. Copy the public key to each slave using the following command in the `.ssh` directory:
    ```bash
    cd .ssh
    cat id_rsa.pub | ssh mpiuser@slave1 "mkdir .ssh; cat >> .ssh/authorized_keys"
    ```
    Repeat the command for each slave.

## NFS Configuration
1. Create a shared folder on the master and each slave:
    ```bash
    mkdir /home/mpiusr/fix
    ```
2. Install NFS on the master:
    ```bash
    sudo apt install nfs-kernel-server
    ```
3. Configure the `/etc/exports` file on the master. Add the following line at the end of the file:
    ```plaintext
    /home/mpiusr/fix *(rw,sync,no_root_squash,no_subtree_check)
    ```
    The Shared Folder location is the directory where the bubble file was created above.
4. Restart NFS Server:
    ```bash
    sudo exportfs -a
    sudo systemctl restart nfs-kernel-server
    ```
5. Install NFS on each slave:
    ```bash
    sudo apt install nfs-common
    ```
6. Mount the shared folder from the master to each slave:
    ```bash
    sudo mount master:/home/mpiusr/fix /home/mpiusr/fix
    ```
    Repeat the command for each slave.

## MPI Installation
1. Install Open MPI on the master and all slaves:
    ```bash
    sudo apt install openmpi-bin libopenmpi-dev
    ```
2. Install the MPI library via pip:
    ```bash
    sudo apt install python3-pip
    pip install mpi4py
    ```

## Running Python Code - Bubble Sort
1. Create a new Python file:
    ```bash
    touch /home/mpiusr/fix/bubble4.py
    ```
2. Navigate to that directory and edit the Python file:
    ```bash
    cd /home/mpiusr/fix
    nano bubble4.py
    ```
    Then create the Python Bubble Sort code. Save by pressing `CTRL + X` and then press `Y`.
    

3. Run the code on the master:
    ```bash
    mpirun -np 4 -host master,slave1,slave2,slave3 python3 bubble4.py
    ```
![WhatsApp Image 2023-11-13 at 09 05 21_b36e7145](https://github.com/tsazaah/MPI-Bubble-Sort/assets/150001965/9a3aa09b-7341-4e05-85e8-58d5a5d86ca5)


If the output like this appears, it has been successful, displaying the output on both the master and slaves, with the output being 4, which is the output from the master, slave1, slave2, and slave3. So what we sorted here is an array: [10, 6, 8, 3, 2, 7, 5, 4, 1, 9] sorted to [1, 2, 3, 4, 5, 6, 7, 8, 9, 10].
