
# Install mysql

```
sudo apt install mysql-server
```

## login mysql

```
sudo mysql

CREATE DATABASE localhost;

CREATE USER 'localhost_user'@'%' IDENTIFIED WITH mysql_native_password BY '12345678';

GRANT ALL ON localhost.* TO 'localhost_user'@'%';

FLUSH PRIVILEGES;
```

## Explain "CREATE USER 'localhost_user'@'%' IDENTIFIED WITH mysql_native_password BY '12345678';"
🔸 ```CREATE USER```
This tells MySQL to create a new user in the database.

🔸 'pickbazar_user'@'%'
'pickbazar_user' → The username you're creating.

'%' → This means the user can connect from any host (any IP address).

You could also use:

'localhost' → if you want the user to connect only from the same machine.

'192.168.1.10' → to allow connection from a specific IP.

🔸 IDENTIFIED WITH mysql_native_password
This part specifies which authentication plugin to use.

mysql_native_password → A widely supported plugin, especially for older applications or systems that require traditional password-based login.

If you're using MySQL 8+, the default might be caching_sha2_password, which can cause issues with some older apps — so specifying mysql_native_password avoids that.

🔸 BY 'pickbazar1'
This sets the password for the user: pickbazar1.

