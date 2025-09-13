# How-to-Install-and-Configure-OpenSSH-9.9p2-on-Ubuntu

---

This guide explains how you can upgrade you existing openSSH to the 9.9p2 by building it from source.

---

## Step 1: Download the Source Code

First, download the latest OpenSSH tarball from the official OpenBSD server:

```bash
wget https://ftp.openbsd.org/pub/OpenBSD/OpenSSH/portable/openssh-9.9p2.tar.gz
```

---

## Step 2: Remove the Existing OpenSSH Installation

Stop and remove the current OpenSSH packages installed through `apt`.

```bash
sudo systemctl stop ssh
sudo apt-get remove openssh-server openssh-client
```

---

## Step 3: Install Build Tools and Dependencies

Update the package list and install the required libraries to compile OpenSSH from source.

```bash
sudo apt update

sudo apt install -y build-essential zlib1g-dev libssl-dev libpam0g-dev libselinux1-dev libwrap0-dev libedit-dev libbsd-dev autoconf automake libtool pkg-config wget curl git
```

---

## Step 4: Build and Install OpenSSH

Extract the downloaded tarball, configure the build, and install it.

```bash
tar zxvf openssh-9.9p2.tar.gz
cd openssh-9.9p2
./configure
make
sudo make install
```

---

## Step 5: Create the Systemd Service File

Ubuntu uses `ssh.service` to manage OpenSSH. Create a new unit file under `/etc/systemd/system/`.

```bash
sudo nano /etc/systemd/system/ssh.service
```

Add the following content:

```
[Unit]
Description=OpenSSH server daemon
After=network.target

[Service]
ExecStart=/usr/local/sbin/sshd -D
ExecReload=/bin/kill -HUP $MAINPID
KillMode=process
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

Save and exit.

---

## Step 6: Reload Systemd and Start the Service

Reload the systemd daemon to recognize the new service, then start and enable SSH.

```bash
sudo systemctl daemon-reload
sudo systemctl start ssh
sudo systemctl enable ssh
```

---

## Step 7: Verify the Installation

Check the service status to confirm it is running.

```bash
systemctl status ssh
```

If you see "active (running)," the setup is complete.

---

### Final Notes

- Always use `ssh.service` on Ubuntu instead of `sshd.service`.
- If you previously masked the old service, you can unmask it with:

```bash
sudo systemctl unmask ssh
```

- The new OpenSSH binary is installed in `/usr/local/sbin/sshd`.

---

### Credit
https://gist.github.com/budiantoip/ad5fd340fd6e226cbf4de2d973113892

https://synaptica.info/en/2024/07/05/9487/
