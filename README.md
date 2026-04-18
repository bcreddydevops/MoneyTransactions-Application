# MoneyTransactions-Application

## User → Load Balancer → Nginx (Frontend) → Node.js (Backend) → MariaDB (Database) ##


## Security Groups (IMPORTANT) ##

## Frontend SG ##
80 → Internet
22 → Your IP

## Backend SG ##
8080 → Frontend SG only

## DB SG ##
3306 → Backend SG only


Test From backend server 
```
telnet <DB-IP> 3306
nc -zv <DB-IP> 3306
```

## Load Balancer (AWS ALB) ##
```
Create Target Group → Frontend EC2
Listener → HTTP (80)
Health Check → /
Attach frontend instance
```

