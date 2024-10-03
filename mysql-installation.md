# How to Install MySQL on Ubuntu 24.04

### Install MySQL
MySQL is available in the default APT repositories on Ubuntu 24.04 and you can install a specific release version using PPA repositories. In the following steps, update the server package index and install the latest MySQL database server version on your server.


#### 1. Update the server package index.
```
sudo apt update
```

#### 2. Install the MySQL server package.
```
sudo apt install mysql-server -y
```
#### 3. View the installed MySQL version on your server.
```
mysql --version
```
Output:
`mysql  Ver 8.0.37-0ubuntu0.24.04.1 for Linux on x86_64 ((Ubuntu))`


### Manage the MySQL System Service
MySQL uses the `mysql` system service to control the database server processes and runtime on your Ubuntu 24.04 server. In the following steps, enable the `mysql` systemd service to start at boot time and verify the service status to manage the database processes on your server.

#### 1. Enable MySQL to automatically start at boot time.
```
sudo systemctl enable mysql
```
Output:
```
Synchronizing state of mysql.service with SysV service script with /usr/lib/systemd/systemd-sysv-install.
Executing: /usr/lib/systemd/systemd-sysv-install enable mysql
```
#### 2. Start the MySQL database server.
```
sudo systemctl start mysql
```

#### 3. View the MySQL server status and verify that it's running.
```
udo systemctl status mysql
```

Output:
```
mysql.service - MySQL Community Server
Loaded: loaded (/usr/lib/systemd/system/mysql.service; enabled; preset: en>
Active: active (running) since Tue 2024-06-04 15:57:55 EEST; 1h 11min ago
Main PID: 7523 (mysqld)
Status: "Server is operational"
Tasks: 38 (limit: 7024)
Memory: 375.5M (peak: 379.3M)
CPU: 2min 32.531s
CGroup: /system.slice/mysql.service
    \u2514\u25007523 /usr/sbin/mysqld

```
## Secure the MySQL Server
Securing the MySQL server is an important step when protecting databases from unauthorized user access. Privileged users such as `root` can access the MySQL database console by default. In the following steps, create a new MySQL `root` user password and disable insecure defaults to secure your database server.

#### 1. Run the following command to start the MySQL secure installation script.
```
sudo mysql_secure_installation
```
Follow the script prompts below to set up a new root user password, remove anonymous users, disallow remote root login, and remove test databases on your MySQL database server.

- **VALIDATE PASSWORD component:** Enter `Y` and press `ENTER` to enable password validation on your server.
- **Password Validation Policy:** Enter **2** to enable the usage of strong passwords on your server.

- **New password:** Enter a new strong password to assign the root database user.
- **Re-enter new password:** Enter your password again to verify the root user password.
- **Do you wish to continue with the password provided?:** Enter `Y` to apply the new user password.
- **Remove anonymous users?:** Enter `Y` to revoke MySQL console access to unknown database users.
- **Disallow root login remotely?:** Enter `Y` to disable remote access to the MySQL root user account on your server.
- **Remove test database and access to it?:** Enter `Y` to delete the MySQL test databases.
- **Reload privilege tables now?:** Enter `Y `to refresh the MySQL privilege tables and apply your new configuration changes.

#### 2. Restart the MySQL service to apply your configuration changes.
```
sudo systemctl restart mysqld
```

## Access MySQL
1. Log in to the MySQL database server as root.
```
sudo mysql -u root -p
```
Enter the root user password you set earlier when prompted.

2. Create a new sample MySQL database. For example, my_database.
```
mysql> CREATE DATABASE my_database;
```
3. Create a new MySQL database user with a strong password. For example, `my_user` and replace `my_password` with your desired password depending on your password strength policy.
```
mysql> CREATE USER 'my_user'@'localhost' IDENTIFIED BY 'my_password';
```

4. Grant the database user `my_user` full privileges to your sample database `my_database`.
```
mysql> GRANT ALL PRIVILEGES ON my_database.* TO 'my_user'@'localhost';
```
5. Refresh the MySQL privilege tables to apply the new user changes.
```
mysql> FLUSH PRIVILEGES;
```
6. Exit the MySQL database console.
```
mysql> Exit
```

### For Remote user access
1. Create user
```
create user 'gon-db-user'@'%' identified by 'Gon$DB&user#2024';

GRANT ALL PRIVILEGES ON . TO 'gon-db-user'@'%' WITH GRANT OPTION;

flush privileges;
```


