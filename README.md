# Debian/Ubuntu Web Server Installation

> Latest Version: 3.0 — June 14, 2017

## About

A custom set of software installation scripts for a Debian 7/8 web server.
Included are scripts for NGINX, MariaDB, MongoDB, MySQL, PHP 5/7, Redis,
CouchDB, Fail2Ban, Monit, XtraBackup, OpenSSL, and creating and setting up
the bash environment, and the firewall using iptables. Everything is optional!

Support for HTTP/HTTPS is also included for NGINX and there are sample config
files for HTTPS domains. All scripts are broken out into separate files if you
want to run them separately, but simply run `./install.sh <profile>` to fire
everything from your config file. To run individual scripts use
`./install.sh <profile> <script1.sh>`. These scripts are optimized to run on a
clean Debian 8 or Ubuntu 16.04 installation and tested heavily on Linode and
AWS. If you have any issues at all, please add them here or message me directly
@mikegioia (https://twitter.com/mikegioia).

## Extremely important SSH notes

SSH is set to run on port 30000 in this setup. If you want to use a different
port (like 22) then edit line 5 of `/src/sshd_config`.

This SSH config looks in `./ssh/authorized_keys` for SSH keys. Edit the
`/src/authorized_keys` file to include any SSH keys for your local machines
to connect directly. **Password authentication is currently enabled** but in
my experience this is unwise. If you want to disable password authentication
then edit line 50 of `/src/sshd_config` to be `PasswordAuthentication no`
and then restart SSH by running `sudo /etc/init.d/ssh restart`. You can include
an `sshd_config` file in any of your environments to overwrite the default
`sshd_config` that will be copied.

## Run the configuration script for each profile

To create a new default profile, run `./configure.sh <profile>` where
`<profile>` is the path hierarchy you want in the `/conf` directory. For
example, to create a new profile named 'development', simply run
`./configure.sh development`. The folder 'development' will be created in
the `/conf` directory with all of the default configuration files.

To create a profile with more context, you could run something like
`./configure.sh dev/app/db1` which would create that path in the `/conf`
directory. In this case, db1 would be the folder with the configuration files.

The main configuration file created will be named `config` which has a few
variables you can set:

* **username**: user account on the web server
* **scripts**: array of scripts to run by default
* **{%program%}Version**: version to install for the given software
* **{%program%}Dependencies**: additional dependencies during software config
* **ipv4Public**: machine's public IP address (optional)
* **ipv4Private**: machines internal network IP address (optional)

## Edit the server configuration files

Inside `/conf/<profile>` are a collection of configuration files and source
files that the applications will use. When you run `./configure.sh <profile>`,
a set of default files will be created in your profile folder. You can edit
and remove these as you see fit. They all _extend_ the base configuration
files. For instance, the local `my.cnf` will be your server-specific MySQL
configuration. If you delete the file, it just won't be copied over during
the MySQL installation.

## Run the installer

When you're ready to install run the command `./install.sh <profile>`
**AS ROOT**. These scripts assume root so please `sudu su` before running them.

## Scripts

> @TODO Write out info on each individual software script
> CouchDB, Fail2Ban, Firewall, MariaDB, MongoDB, Monit, MySQL, NGINX, OpenSSL,
> PHP 5.6, PHP 7, Profile, SSH, User, XtraBackup

## Notes about this installation

* This script will `apt-get update` and `apt-get upgrade` your system. This
  could take a while so be sure to watch over it.
* You will be prompted to set passwords for MySQL and MariaDB. Keep those handy
  and watch when it prompts.
* You will be asked to install extensions if you run the PHP script. These are
  all optional.
* You will be asked if you want to overwrite the SSH config each time the
  profile script runs. It will default to NO but it's best to copy this over the
  first time you run it.
* It's my practice to `git clone` this repo (or fork) to every server as the
  regular user. This way, I can `git pull` changes without needing to `sudo`.
  Then, I `sudo su` before running the installer.
* I've timed the entire install process and it averages to about 8 minutes on a
  512 MB machine!
