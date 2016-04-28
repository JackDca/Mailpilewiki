When Mailpile is adopted by non-technical "casual users" they will expect easy access to their email from remote devices, including cell phones and tablets.  In this scenario, a major security issue is password strength.  Casual users often use weak passwords, especially since repeatedly entering a strong password on a hendheld device can be onerous.

The objective of the code documented here is to enable links between remote devices on the Internet and a Mailpile server, that do not expose a password-authenticated interface to the Internet.  Password authentication is used once from the remote, in a scripted procedure which sets up public key authentication for all subsequent accesses.  The Mailpile server is assumed to be a computer or "personal cloud" appliance located at the user's home.  The remote could be a cell phone, tablet, or another computer.  This procedure in effect makes the remote device a semi-permanent extension of the server, with restricted capabilities.

This is a work in progress.  It is implemented as shell scripts and requires the user to key in some shell commands.  It is just a first step on the path to possibly creating device apps and a Mailpile interface that will handle the procedure automatically so that casual users can do it easily.

This code has been tested using a Debian Jessie Gnu-Linux system for the server, and an Asus TF700t Android tablet, rooted, with the Cyanogenmod "ROM", as the remote.  (However, the code should not require root access.)  One reason for posting this documentation is to get others to test the scripts on other systems.  Let me know what happens - Jack Dodds <brmdamon@hushmail.com>

The test system uses a dynamic DNS service to make it acessible from the Internet.  The router is set up to forward the port used by the SSH server to the Mailpile server.  This is too complicated for a casual user.  The use of a relay (Pagekite?) or STUN are friendlier possibilities.

### Why OpenSSH?

OpenSSH has all the capabilites that are needed to implement an automatic secure link between the Mailpile server and a remote, without unnecessary bloat.  It is well maintained and vulnerabilities are patched quickly when they are discovered.

Another possible approach is SSL/TLS.  It is built into the browser, which might simplify implementation.  To simplify setup it could be used with self-signed certificates.  It would be necessary to use client authentication, which might create some complications or vulnerabilites.  I don't know OpenSSL well enough to compare the two approaches.

Still another possible approach is OpenVPN.

### How it works

The user first runs a "server setup script" on the Mailpile server which creates a configuration subdirectory (`$MAILPILE_HOME/sshd`) for an OpenSSH server on the Mailpile server.  The server setup script is run only once.

To configure a remote to link to the server, the user runs a "remote setup script" on the Mailpile server.  The script randomly selects a high port number (20000-30000) and runs the OpenSSH server to listen on it.  The script then instructs the user to key in commands in a shell on the remote.  The commands on the remote run an OpenSSH client, connect to the server, verify the server fingerprint, authenticate using the user's server password and create a reverse SSH tunnel giving the server script a shell on the remote.  The server script uses the remote shell to copy the server identification key to the remote, create an identification key for the remote, copy its public part to the server and create scripts on the remote that can be used to manage SSH tunnels from a local port on the remote to the Mailpile local port on the server.  The remote setup script is run only once for each remote.  Multiple remotes can be set up for the same server.

Once setup is complete the user can run a script on the Mailpile server to start an OpenSSH server  that listens on the previously selected port number for connections from remotes.  Connections are automatic and use public key authentication.  The OpenSSH server runs as an unprivileged user.  The use of a high port number hopefully avoids the blitz of brute force attacks that are attracted if port 22 is open.  The server configuration file restricts the use of the SSH tunnel so that it can only be used to forward the local Mailpile port on the remote to the local Mailpile port on the server.  No shell access to the server is ever permitted.  The configuration file also restricts the choice of crypto algorithms to those that are considered most secure.

On the remote, the user can run a script that starts the OpenSSH client in a background process that retries once a minute if the connection is broken, so it needs to be run only once when the remote is powered up.

### Requirements

On the Mailpile server, the OpenSSH server sshd is required.  On the remote, Jack Palevich's app "Terminal Emulator", the app "Busybox", the OpenSSH client (`ssh`) and the key generator (`ssh-keygen`) are required.  Both the apps are free software available on F-Droid or the Google App Store.  OpenSSH is provided as part of CyanogenMod (in `/system/bin` ) and is (mostly) under the OpenBSD licence, but is not normally present in stock devices.

One possible way to get `ssh` and `ssh-keygen` for a stock device is to download a Cyanogenmod image (e.g. `https://download.cyanogenmod.org/get/jenkins/90492/cm-11-20141112-SNAPSHOT-M12-tf700t.zip`) and then to extract `/system/bin/ssh` and `/system/bin/ssh-keygen` to the home directory `~` of Terminal Emulator.  (It is possible that these executables may require shared libraries which would also need to be extracted - this is not tested.)  This is OK for experimental work but it could be a violation of the licence to distribute programs obtained in this way.  Thanks to the good people of Cyanogenmod for their work.  Future task:  create a free Android app that provides all the OpenSSH executables.

### Specific setup instructions

--[Using a Linux Mailpile server for SSH tunneling from remotes](#using-a linux-mailpile-server-for-ssh-tunneling-from-remotes)
- [Setting up an Android remote device for SSH tunneling to a Mailpile server](#setting-up-an-android-remote-device-for-ssh-tunneling-to-a-mailpile-server)
- [Setting up a Windows Mailpile server for SSH tunneling from remotes](#setting-up-a-Windows-mailpile-server-for-ssh-tunneling-from-remotes)
- [Setting up a Windows remote system for SSH tunneling to a Mailpile server](#setting-up-a-windows-remote-system-for-ssh-tunneling-to-a-mailpile-server)
- [Using an Apple server for SSH tunneling from remotes](#using-an-apple-server-for-ssh-tunneling-from-remotes)
- [Setting up an iPad or iPhone remote for SSH tunneling to a Mailpile server](#setting-up-an-ipad-or-iphone-remote-for-ssh-tunneling-to-a-mailpile-server)


### Setting up a Linux Mailpile server for SSH tunneling from remotes

Install (`apt-get`) the Debian `ssh` package, or equivalent on other distros, if not already installed.

Open a terminal window, cd to the Mailpile data directory, e.g. `~/.local/share/Mailpile/default` .

Run the script `.../Mailpile/scripts/mp_ssh_server_setup.sh` .  (The `...` indicates that the path will depend on the location of your Mailpile code.) This will create a subdirectory `sshd` in the Mailpile data directory, which will look something like this:

```
$ ls -l sshd
-rw-r--r-- 1 jack jack    0 apr 25 10:29 authorized_keys
-rw-r--r-- 1 jack jack    6 apr 25 10:29 port_external
-rw-r--r-- 1 jack jack  746 apr 25 10:29 remote_known_hosts
-rw-r--r-- 1 jack jack 2089 apr 25 10:29 sshd_config
-rw------- 1 jack jack  399 apr 25 10:29 ssh_host_ed25519_key
-rw-r--r-- 1 jack jack   92 apr 25 10:29 ssh_host_ed25519_key.pub
-rw------- 1 jack jack 3243 apr 25 10:29 ssh_host_rsa_key
-rw-r--r-- 1 jack jack  736 apr 25 10:29 ssh_host_rsa_key.pub
```

If the server is behind a router, the router must now be set up to forward the high port number to the Mailpile server.  The port number is in the file port_external listed above:

```
$ cat sshd/port_external
25610
```

Once this step is complete, set up the remote (next section); then:

To start listening, use the script `.../Mailpile/scripts/mp_ssh_server_start.sh` .

To stop listening and terminate connections, use the script `.../Mailpile/scripts/mp_ssh_server_stop.sh` .

For all these scripts, the current directory must be the Mailpile data directory, or alternatively, the environment variable `MAILPILE_HOME` must be set to point to the Mailpile data directory. e.g. 

```
export MAILPILE_HOME=~/.local/share/Mailpile/default
```

### Setting up an Android remote device for SSH tunneling to a Mailpile server

For setup, install "Terminal Emulator" and "Busybox".  If necessary, install `ssh` and `ssh-keygen` - details will depend on how you do this.  The "Hacker's Keyboard" app is also highly recommended since the standard Android keyboard makes it hard to enter special characters. 

Open a "Terminal Emulator" window.  When the window is first opened, the current directory is NOT the home directory of the Terminal Emulator user - so do `cd ~` to make things less confusing.

Then, on the server, `cd` to the Mailpile data directory, then run the remote setup script giving the Internet address of the Mailpile server as the parameter, e.g. 

```
 .../Mailpile/scripts/mp_ssh_remote_setup.sh   cyberspirit.dyndns.org
```

Key in commands to the remote Terminal Emulator window as instructed by the server script.  When the script is complete on the server, exit the busybox nc command on the remote using <ctrl>c.  A directory ~/ssh-tunnel should be present on the remote and accessible from the Terminal Emulator shell.  It will look something like this:

```
$ ls -l ssh-tunnel
-rw------- u0_a41   u0_a41       1732 2016-04-25 11:01 config25610
-rw------- u0_a41   u0_a41       3243 2016-04-25 11:02 id_rsa
-rw------- u0_a41   u0_a41        754 2016-04-25 11:02 id_rsa.pub
-rw------- u0_a41   u0_a41        746 2016-04-25 11:02 known_hosts25610
-rwx------ u0_a41   u0_a41        171 2016-04-25 11:03 run25610
-rwx------ u0_a41   u0_a41         54 2016-04-25 11:03 start25610
-rwx------ u0_a41   u0_a41        249 2016-04-25 11:03 stop25610
```

Once setup is complete, to open a connection, use the script `~/ssh-tunnel/startnnnnn` on the remote, where `nnnnn` is the external port selected by the server setup script (that's the `25610` that appears in some of the file names listed above).  The Terminal Emulator window can then be closed.  The SSH client is run by a background process that retries once a minute if the Mailpile server has not been started, or if a connection is lost. 

Then, whenever Mailpile access is required, open it in the usual way using the browser at `127.0.0.1:33411` (or other local port, same as used on the server) .

To terminate a connection, use the script `~/ssh-tunnel/stopnnnnn`.

### Setting up a Windows Mailpile server for SSH tunneling from remotes

(To be tested - watch this space!): Install `cygwin` with the `sshd` package.  Use the same scripts as for Linux.

### Setting up a Windows remote system for SSH tunneling to a Mailpile server

(To be tested - watch this space!): Install `cygwin` with the `ssh` package.

### Using an Apple server for SSH tunneling from remotes

 -- Help, anyone? --.

### Setting up an iPad or iPhone remote system for SSH tunneling to a Mailpile server

 -- Help, anyone? --.


