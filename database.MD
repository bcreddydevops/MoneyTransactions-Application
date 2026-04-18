# Install MariaDB

```
dnf install mariadb105-server -y
systemctl enable mariadb
systemctl start mariadb
```

Run the below command for Secure Installation
```
mysql_secure_installation
```
just enter by default and it will ask new password give your password what ever you want . i set password is MoneyAPP@1
```
MoneyAPP@1
```
# Verification 

```
[root@ip ~]# mysql -u root -pMoneyAPP@1
```

# Create Database & Table

```
CREATE DATABASE money_app;
USE money_app;

CREATE TABLE transactions (
    id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(100),
    amount DECIMAL(10,2),
    type VARCHAR(10),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

## Allow Remote Access (IMPORTANT)

```
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'MoneyAPP@1';
FLUSH PRIVILEGES;
```

Now check the databases using below commands and select the table 

```
show databases;
```

select table and check the transactions 

```
use money_app;
```

```
select * from transactions;
```

Insert one record manually

```
INSERT INTO transactions (title, amount, type)
VALUES ('Salary', 50000, 'credit');
```

now check:

```
select * from transactions;
```


