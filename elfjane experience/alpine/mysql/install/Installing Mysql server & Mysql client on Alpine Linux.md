
# Installing Mysql server & Mysql client on Alpine Linux
###### tags: `alpine`, `mysql`, `linux`
<https://wiki.alpinelinux.org/wiki/Mysql>
<http://yahoo.com>

## 1
## 2
---

### 4

---
## Installation:
The Alpine Linux repositories no longer include the actual MySQL binaries, installing the mysql-* packages will instead install MariaDB.

Installing mariadb will create the user mysql. When the database is initialized, two users will be added to the database: root and mysql. By default these users will only be accessible if you are logged in as the corresponding system user.

```
apk add mysql mysql-client
```

```bash
console:~$ apk add mysql mysql-client
(1/17) Installing mariadb-common (10.6.10-r0)
(2/17) Installing libaio (0.3.112-r2)
(3/17) Installing ca-certificates (20220614-r0)
(4/17) Installing brotli-libs (1.0.9-r6)
(5/17) Installing nghttp2-libs (1.47.0-r0)
(6/17) Installing libcurl (7.83.1-r5)
(7/17) Installing libgcc (11.2.1_git20220219-r2)
(8/17) Installing xz-libs (5.2.5-r1)
(9/17) Installing linux-pam (1.5.2-r0)
(10/17) Installing pcre2 (10.40-r0)
(11/17) Installing libstdc++ (11.2.1_git20220219-r2)
(12/17) Installing libxml2 (2.9.14-r2)
(13/17) Installing mariadb (10.6.10-r0)
Executing mariadb-10.6.10-r0.pre-install
(14/17) Installing mariadb-openrc (10.6.10-r0)
(15/17) Installing mysql (10.6.10-r0)
(16/17) Installing mariadb-client (10.6.10-r0)
(17/17) Installing mysql-client (10.6.10-r0)
Executing busybox-1.35.0-r14.trigger
Executing ca-certificates-20220614-r0.trigger
OK: 191 MiB in 52 packages
console:~$
```

## Initialization:
The version of MariaDB in the Alpine repositories behave like the MySQL tarball. No graphical tools are included.

The datadir located at /var/lib/mysql must be owned by the mysql user and group. The location of the datadir can be changed by editing the mariadb service file in /etc/init.d. The new location will also need to be set by adding datadir=<YOUR_DATADIR> in the [mysqld] section in a mariadb configuration file.

Normal initialization of mariadb can be done as follows:

1. Initialize the main mysql database, and the data dir as standardized to /var/lib/mysql by running /etc/init.d/mariadb setup
2. Start the main service. At this point there will be no root password set. /etc/init.d/mariadb start
3. Secure the database by running mysql_secure_installation
4. Setup permissions for managing others users and databases

```bash
console:~$ /etc/init.d/mariadb setup
 * Creating a new MySQL database ...Installing MariaDB/MySQL system tables in '/var/lib/mysql' ...
OK

To start mysqld at boot time you have to copy
support-files/mysql.server to the right place for your system


Two all-privilege accounts were created.
One is root@localhost, it has no password, but you need to
be system 'root' user to connect. Use, for example, sudo mysql
The second is mysql@localhost, it has no password either, but
you need to be the system 'mysql' user to connect.
After connecting you can set the password, if you would need to be
able to connect as any of these users with a password and without sudo

See the MariaDB Knowledgebase at https://mariadb.com/kb

You can start the MariaDB daemon with:
cd '/usr' ; /usr/bin/mysqld_safe --datadir='/var/lib/mysql'

You can test the MariaDB daemon with mysql-test-run.pl
cd '/usr/mysql-test' ; perl mysql-test-run.pl

Please report any problems at https://mariadb.org/jira

The latest information about MariaDB is available at https://mariadb.org/.

Consider joining MariaDB's strong and vibrant community:
https://mariadb.org/get-involved/

 [ ok ]
console:~$
```
## Enable the mariadb service at boot time:
```bash
console:~$ rc-update add mariadb
* service mariadb added to runlevel default
console:~$
```
## Configuratione:
In order to help with the basic configuration of the database engine, MariaDB provides mysql_secure_installation. This script walks you through the basics of securing the database. The options are explained below.

1. **Enter current password for root (enter for none)**: If you have previously set up a root password, provide it here and press enter. If not, just press enter.
2. **Switch to unix_socket authentication [Y/n]** Setting the root password or using the Unix_socket ensures that only admins can log into engine database. For non-production servers just press "n" to setup a root password, which will give you the response ... skipping.
3. **Change the root password? [Y/n]** Here you can change the root password, or set one if needed. Press "Y" and enter the new password.
4. **Remove anonymous users? [Y/n]** Remove anonymous users created to log in using socket authentication. Unless you're sure you need this, answer "Y" to remove them.
5. **Disallow root login remotely? [Y/n]** Normally, root should only be allowed to connect from 'localhost' in order to protect from password sniffing attempts over the network. Answer "Y".
6. **Remove test database and access to it? [Y/n]** By default, MariaDB comes with a database named 'test' that anyone can access. If this is not needed, answer "Y".
7. **Reload privilege tables now? [Y/n]** Reloading the privilege tables will ensure that all changes made so far will take effect immediately. Answer "Y".
After the script exits, restart the service with rc-service mariadb restart

```bash
console:~$ mysql_secure_installation

NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!

In order to log into MariaDB to secure it, we'll need the current
password for the root user. If you've just installed MariaDB, and
haven't set the root password yet, you should just press enter here.

Enter current password for root (enter for none): 
OK, successfully used password, moving on...

Setting the root password or using the unix_socket ensures that nobody
can log into the MariaDB root user without the proper authorisation.

You already have your root account protected, so you can safely answer 'n'.

Switch to unix_socket authentication [Y/n] 
Enabled successfully!
Reloading privilege tables..
 ... Success!


You already have your root account protected, so you can safely answer 'n'.

Change the root password? [Y/n] 
New password: 
Re-enter new password: 
Password updated successfully!
Reloading privilege tables..
 ... Success!


By default, a MariaDB installation has an anonymous user, allowing anyone
to log into MariaDB without having to have a user account created for
them.  This is intended only for testing, and to make the installation
go a bit smoother.  You should remove them before moving into a
production environment.

Remove anonymous users? [Y/n] 
 ... Success!

Normally, root should only be allowed to connect from 'localhost'.  This
ensures that someone cannot guess at the root password from the network.

Disallow root login remotely? [Y/n] 
 ... Success!

By default, MariaDB comes with a database named 'test' that anyone can
access.  This is also intended only for testing, and should be removed
before moving into a production environment.

Remove test database and access to it? [Y/n] 
 - Dropping test database...
 ... Success!
 - Removing privileges on test database...
 ... Success!

Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.

Reload privilege tables now? [Y/n] 
 ... Success!

Cleaning up...

All done!  If you've completed all of the above steps, your MariaDB
installation should now be secure.

Thanks for using MariaDB!
console:~$
```

## To start the database daemon on every boot, run rc-update add mariadb default
```bash
console:~$ rc-update add mariadb
* service mariadb added to runlevel default
console:~$
```

## Configuration files and customization
Rather than being stored in my.cnf, configuration settings for MariaDB are now organized in separate files. The primary configuration is done by adding files to /etc/my.cnf.d/. User-specific configuration files are stored in ~/.my.cnf. User-specific configuration files are loaded after the system-wide configuration. The locations of the various configuration files are listed below.


| Config file        | Path and name                    | Versions of Alpine | Contents to configure                     |
| ------------------ | -------------------------------- | ------------------ | ----------------------------------------- |
| my.cnf             | /etc/mysql/my.cnf                | v2 to v3.8         | All the directives, global config file    |
| mariadb-server.cnf | /etc/my.cnf.d/mariadb-server.cnf | since 3.9          | First global config file, main directives |
| .my.cnf            | $HOME                            | all                | user name only config directives          |
As previously mentioned, this page describes basic usage of MariaDB. For professional usage, [Production DataBases : mysql](https://wiki.alpinelinux.org/wiki/Production_DataBases_:_mysql) should also be referenced.

* The following command will configure the server to accept all incoming connections. This should only be done for development, or if the database is not exposed to the Internet or a sensitive network.

```
sed -i "s|.*bind-address\s*=.*|bind-address=0.0.0.0|g" /etc/mysql/my.cnf
sed -i "s|.*bind-address\s*=.*|bind-address=0.0.0.0|g" /etc/my.cnf.d/mariadb-server.cnf
```

* For simple installations, disabling hostname search can improve performance, but is only useful for local servers.

```
sed -i "s|.*skip-networking.*|skip-networking|g" /etc/mysql/my.cnf
sed -i "s|.*skip-networking.*|skip-networking|g" /etc/my.cnf.d/mariadb-server.cnf
```
## Updating or coming from upgrading
When upgrading between Alpine Linux releases, MariaDB may also have a major version change, and the databases should be upgraded to match. The recommended steps in this process are detailed below.

1. While it may no longer be strictly necessary, it's useful to backup your databases before upgrading the database version.
2. Update Alpine Linux and the MariaDB/MySQL packages.
3. Run mysql_upgrade -u root -p script, and provide the password for the root database user.
4. Restart the service by running rc-service mariadb restart.

If mysql_upgrade fails because MySQL cannot start, try running MySQL in safemode with mysqld_safe --datadir=/var/lib/mysql/, and then run mysql_upgrade -u root -p again.

## Connect mysql server
```bash
console:~$ mysql -u root -p
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 13
Server version: 10.6.10-MariaDB MariaDB Server

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> 
console:~$
```