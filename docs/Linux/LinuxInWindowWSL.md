The WSL window subsystem for linux to install. In cmd we run the command to see the version and work.

```bash
wsl --version
WSL version: 3.0.3.0
Kernel version: 6.18.54.1-1
WSLg version: 1.0.79
MSRDC version: 1.2.7391
Direct3D version: 1.611.1-81528511
DXCore version: 10.0.26100.1-240331-1435.ge-release
Windows version: 10.0.19045.6466

wsl -l -v
  NAME              STATE           VERSION
* Ubuntu            Stopped         2
  docker-desktop    Stopped         2
```

The Ubuntu app when started to run the status changed to running. The Ubuntu is an command prompt. The Ubuntu command prompt can be started and also in cmd we connect `wsl  -d Ubuntu`.

The command `wsl --list --online` show all the linux distribution and install the linux in the system.

To install `wsl --install -d Ubuntu-24.05` the name should be in the distribution.

In the cmd we write wsl then it connect to the linux and we can code and in ubuntu command prompt it is also connected.

```bash
sdeb@DESKTOP-PUEA9OO:~$ lsb_release -a
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 22.04.2 LTS
Release:        22.04
Codename:       jammy
```

In the Ubuntu  application we need to do the update

```bash
sudo apt update
```

It will ask for password. In case to forgot pwd make a new password.

When the update is done then `sudo apt upgrade`.

**Forgot Password.**

The Powershell in administrative and then `wsl -d Ubuntu -u root` the root user does not need any pwd and then `passwd sdeb` and set the new password.



Update the apt.

`sudo apt-get update && sudo apt-get -y upgrade`

Install wget `sudo apt install wget -y`

Install xrdp using the bash script `sudo wget -qO - https://bonguides.com/wsl/xrdp | bash`

Install taskel and it will help to install desktop environment.

`sudo apt install tasksel`

In window Win + R then mstsc

### Get the linux desktop experience in the window.

It can be done in the WSL 2 set using XFCE. The current version Ubuntu 22.04.5 LTS.

```bash
sdeb@DESKTOP-PUEA9OO:~$ lsb_release -a

No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 22.04.5 LTS
Release:        22.04
Codename:       jammy
``` . 

```bash

sudo apt update

# This installs the graphical desktop and terminal.
sudo apt install xfce4 xfce4-terminal dbus-x11 -y 
```

WSL 2 runs the graphical Linux application through WSLg and a complete desktop session needs a desktop session approach. An option is to use an RDP server.
```bash
sudo apt install xrdp -y 
# Install the RDP server.

# Run the command and configure the XFCE to login.
sdeb@DESKTOP-PUEA9OO:~$ echo "startxfce4" > ~/.xsession
sdeb@DESKTOP-PUEA9OO:~$ sudo service xrdp start
```
Connect to the Linux desktop. In window Win + R then mstsc and in Remote Desktop Connection the localhost:3389 connection and then signin with the sdeb and pwd.

In case of the remote desktop open and stops then some command.
```bash
sdeb@DESKTOP-PUEA9OO:~$ echo "xfce4-session" > ~/.xsession
sdeb@DESKTOP-PUEA9OO:~$ chmod +x ~/.xsession

# Fix the XRDP startup script.

sdeb@DESKTOP-PUEA9OO:~$ sudo nano /etc/xrdp/startwm.sh

```
In the file the last like like the test and exec to comment and put some line. Comment the lines in the end.
```
test -x /etc/X11/Xsession && exec /etc/X11/Xsession
exec /bin/sh /etc/X11/Xsession
```
In the comment code place the code.

```yaml
unset DBUS_SESSION_BUS_ADDRESS
unset XDG_RUNTIME_DIR
exec startxfce4
```
Restart the service.
```yaml
sdeb@DESKTOP-PUEA9OO:~$ sudo service xrdp restart
```

In the linux desktop opens and open the terminal to install cromium browser.

```yaml
sudo apt install chromium-browser -y

# In case it is taking time or anything we can install the google chrome .deb package and it will avoid installing cromium through snap.


dpkg --print-architecture
# amd64.
sudo apt install wget -y
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
sudo apt install ./google-chrome-stable_current_amd64.deb -y

google-chrome
# It will open chrome.
```