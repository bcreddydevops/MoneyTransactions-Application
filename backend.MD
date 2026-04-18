## Install Node.js

Backend service is responsible for adding the given values to database. Backend service is written in NodeJS, Hence we need to install NodeJS.

```
dnf module disable nodejs -y
dnf module enable nodejs:20 -y
dnf install nodejs -y
```

## Create App User

```
useradd --system --home /app --shell /sbin/nologin expense
mkdir /app
cd /app
```

## Install Dependencies

```
npm init -y
npm install express mysql2 cors
```

## Backend Code (/app/index.js)

```
const express = require("express");
const mysql = require("mysql2");
const cors = require("cors");

const app = express();
app.use(cors());
app.use(express.json());

const db = mysql.createConnection({
  host: process.env.DB_HOST,
  user: "root",
  password: "MoneyAPP@1",
  database: "money_app"
});

db.connect((err) => {
  if (err) {
    console.error("DB Connection Failed:", err);
    process.exit(1);
  }
  console.log("Connected to DB");
});

app.get("/transactions", (req, res) => {
  db.query("SELECT * FROM transactions", (err, result) => {
    if (err) return res.send(err);
    res.send(result);
  });
});

app.post("/transactions", (req, res) => {
  const { title, amount, type } = req.body;
  db.query(
    "INSERT INTO transactions (title, amount, type) VALUES (?, ?, ?)",
    [title, amount, type],
    (err, result) => {
      if (err) return res.send(err);
      res.send(result);
    }
  );
});

app.listen(8080, "0.0.0.0", () => {
  console.log("Server running on port 8080");
});
```

## Create Systemd Service

```
vim /etc/systemd/system/backend.service
```

```
[Unit]
Description=Backend Service

[Service]
User=expense
Environment=DB_HOST=<Private-DBEC2-IP>
ExecStart=/bin/node /app/index.js
Restart=always

[Install]
WantedBy=multi-user.target
```

**NOTE: Ensure you replace <MYSQL-SERVER-IPADDRESS> with IP address**

## Start Backend

```
chown -R expense:expense /app
systemctl daemon-reload
systemctl enable backend
systemctl start backend
```
