# Astroconda IRAF Virtual Machine

As operating systems evolve unmaintained software has a tendency to break. If you are in a situation where IRAF is no longer usable, and you are still dependent upon it for one reason or another, we have created a CentOS 7 virtual appliance with everything pre-installed.

On `[DATE]` STScI will cease providing software support for the following products:

* Python 2.7
* PyRAF
* IRAF


## Appliance Support

* None
* Use at your own risk


## Disclaimer

THE SOFTWARE (AND APPLIANCE) IS PROVIDED "AS IS" AND THE AUTHOR DISCLAIMS ALL WARRANTIES WITH REGARD TO THIS SOFTWARE INCLUDING ALL IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS. IN NO EVENT SHALL THE AUTHOR BE LIABLE FOR ANY SPECIAL, DIRECT, INDIRECT, OR CONSEQUENTIAL DAMAGES OR ANY DAMAGES WHATSOEVER RESULTING FROM LOSS OF USE, DATA OR PROFITS, WHETHER IN AN ACTION OF CONTRACT, NEGLIGENCE OR OTHER TORTIOUS ACTION, ARISING OUT OF OR IN CONNECTION WITH THE USE OR PERFORMANCE OF THIS SOFTWARE.


# Install VirtualBox

## Linux

### Debian / Ubuntu
```bash
$ sudo apt-get update
$ sudo apt-get install virtualbox
```

### Fedora
```bash
$ sudo dnf install virtualbox
```

### CentOS
```bash
$ sudo yum install virtualbox
```

### Arch
```bash
$ sudo pacman -S virtualbox
```

## MacOS

[Click Here](https://www.virtualbox.org/wiki/Downloads)

## Windows

### 7 / 8 / 10
[Click Here](https://www.virtualbox.org/wiki/Downloads)


# Download the Appliance (OVA format)

[Click Here](https://ssb.stsci.edu/aciraf/astroconda_iraf_centos7.ova)

-or-

```bash
curl -LO https://ssb.stsci.edu/aciraf/astroconda_iraf_centos7.ova
```

# Import the Appliance

### From a Shell

```bash
$ VirtualBox astroconda_iraf_centos7.ova
```

### From a File Manager

Double-click the `astroconda_iraf_centos7.ova` file.


No matter if you install via the shell or file manager a dialog will appear requesting that you specify the (new) location of the virtual machine. It is also possible to adjust the virtual machine's settings (listed below) here by double-clicking on an item. However, if you prefer to follow along with this guide simply click `Import` now.

The import operation may take several minutes to complete, especially on Windows. Be patient.

# Post-Installation

## Generate a custom Virtuabox Network

1. On the leftmost pane of the VirtualBox main window, click to select `Tools`
2. Click hamburger menu icon, and select `Network`
3. Click `Create` icon. An entry will appear: `vboxnet#` (# will be an integer value)
4. (optional) To return, click hamburger menu icon again, and select `Welcome`

## Configure the Virtual Machine

The defaults applied to this VM are the bare mininum required by CentOS:

* CPU: 1
* RAM: 512MB
* VRAM: 8MB

1. In the leftmost pane select `astroconda_iraf_centos7` from the list.
2. Right-click to open the context menu, then click on `Settings`.

### Set Base Memory

1. Click `System`
2. Adjust `Base Memory` slider to a value appropriate for your system (Recommended: `TOTAL_RAM/4` or `TOTAL_RAM/2`)

### Set CPU Count

1. Click `System`
2. Click the `Processor` tab
3. Adjust slider to set a value appropriate for your system. (Recommended: `TOTAL_CPUS-1` or `TOTAL_CPUS/2`)

### Set Video Memory

1. Click `Display`
2. Adjust slider to set a value approprate for your system. (Recommended: `MAX`)

### Create a host-only network adapter

1. Click `Network`
2. Click `Adapter 2` tab
3. Enable `Enable Network Adapter` check-box
4. From `Attached to` drop-down menu select `Host-only Adapter`
5. From `Name` drop-down menu select `vboxnet0`

### Create a Shared Folder

To make it easier to interact with data from within the VM you will want to share a directory on your local system. VirtualBox uses the CIFS protocol to acheive this, so it's important to note the permissions of files and directories created here will be `770` (`rwxrwx---`) by default.

1. Click `Shared Folders`
2. Click `Folder with a plus sign` icon
3. Set `Folder Path` to the location of your data (e.g. `/home/username/my_data`)
4. Set `Folder Name` (can be anything)
5. Enable `Auto-mount` check-box
6. Set `Mount point` to a path within the VM (e.g. `/mnt/data`, `/data`, `/home/aciraf/mydata`, etc)
7. Enable `Make Permanent` check-box
8. Click `OK`


### Finished with Settings

1. To save your settings, click `OK`


# Start the Appliance

1. On the leftmost pane, Double-click on `astroconda_iraf_centos7`
2. At the login prompt, authenticate with:

**Username:** `aciraf`
**Password:** `aciraf`

3. [If using `Shared Folders`] Add `aciraf` user to the `vboxsf` group

For your security, the `aciraf` user is not capable of accessing shared folders by default:

```bash
$ sudo usermod -G vboxsf -a aciraf
$ exit
# <Log in again>
```

# Accessing Data (via `Shared Folders`)

Assuming you configured your `Shared Folders` mount-point as `/mnt/data`

4. Verify mount-point works as expected
```bash
$ ls -l /mnt/data
# Expectation: You see your data

$ echo test > /mnt/data/testfile.txt
# Expectation: You can write to the mount-point

$ cat /mnt/data/testfile.txt
test
# Expectation: You can read from the mount-point

$ rm /mnt/data/testfile.txt
# Expectation: You can delete data from the mount-point
```


# Accessing Data (via SSHFS)

This method only works when the remote system is configured to accept logins over `ssh`. The `vboxnet#` network you created earlier uses the default subnet `192.168.56.1/24`. The default gateway assigned to this network, `192.168.56.1`, leads back to your host system. If the data is not stored on your host system you should supplement the IP below with the correct hostname.

## Install `sshfs`
```bash
$ yum install -y sshfs
```

## Create a directory to act as a local mount-point
```bash
$ mkdir ~/remote_data
```

## Mount your data directory to the local mount-point
```bash
$ sshfs username@192.168.56.1:/home/username/my_data ~/remote_data
username@192.168.56.1's password: [authenticate here]
```

## Verify the mount-point works as expected
```bash
$ ls -l ~/remote_data
# Expectation: You see your data

$ echo test > ~/remote_data/testfile.txt
# Expectation: You can write to the mount-point

$ cat ~/remote_data/testfile.txt
test
# Expectation: You can read from the mount-point

$ rm ~/remote_data/testfile.txt
# Expectation: You can delete data from the mount-point
```

# Getting Graphical

This virtual machine is not configured for displaying graphics. In order to render graphics you will need:

1. Xorg/X11 server (Windows: [VcXsrv](https://sourceforge.net/projects/vcxsrv).)
2. `ssh` client (Windows: [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty) or [KiTTY](http://kitty.9bis.net))


## What is your virtual machine's host-only IP?

The second network adapter you enabled earlier will be initialized as `eth1` by the Linux kernel. You already know what the subnet will be, so go ahead and poll it with: 

```bash
$ ifconfig eth1 | grep 192.168.56
        inet 192.168.56.101 # ... <- this is the IP of the adapter
```

## SSH to your virtual machine (with X11 forwarding)

### Linux / Darwin
```bash
$ ssh -X aciraf@192.168.56.101
# password: aciraf
$ cd /mnt/data
$ pyraf
# Expectation: Graphical splash screen is diplayed
```

### Windows

1. Start your local X11 server
2. Enable X11 forwarding for your client (do not specify a custom DISPLAY; it will be automatically assigned at login)
3. Set username to: `aciraf`
4. Set password to: `aciraf`
5. Set the remote host to: `192.168.56.1`
6. [optional] Save your connection details to avoid repeating this in the future
7. Connect

```bash
$ cd /mnt/data
$ pyraf
# Expectation: Graphical splash screen is displayed
```
