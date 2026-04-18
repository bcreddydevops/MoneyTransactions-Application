# Frontend

The frontend  service in money tranasactions to serve the web content over Nginx. This will have the web frame for the web application.
This is a static content and to serve static content we need a web server. 

Install Nginx
```
dnf install nginx -y 
```
Enable nginx
```
systemctl enable nginx
```
Start nginx
```
systemctl start nginx
```

**Try to access the service once over the browser and ensure you get some default content**

```
http://<nginx-public-ip>:80
```

## Remove the default content that web server is serving. ##
```
rm -rf /usr/share/nginx/html/*
```

## Create UI ##

```
vim /usr/share/nginx/html/index.html
```

Add your HTML UI Code

```
vim /usr/share/nginx/html/index.html
```

```
<!DOCTYPE html>
<meta charset="UTF-8">
<html>
<head>
  <title>Money Transactions</title>

  <style>
    body {
      font-family: Arial, sans-serif;
      background: linear-gradient(to right, #667eea, #764ba2);
      text-align: center;
      color: white;
    }

    /* Big Branding */
    .brand {
      font-size: 50px;
      font-weight: bold;
      color: #00ffcc;
      text-shadow: 2px 2px 8px black;
      margin-top: 20px;
    }

    .app-title {
      font-size: 30px;
      color: yellow;
      margin-bottom: 20px;
    }

    .container {
      background: white;
      color: black;
      width: 50%;
      margin: auto;
      padding: 20px;
      border-radius: 10px;
      box-shadow: 0px 0px 10px black;
    }

    input, select {
      padding: 10px;
      margin: 5px;
      border-radius: 5px;
      border: 1px solid #ccc;
    }

    /* Add Button */
    .add-btn {
      background: green;
      color: white;
      padding: 10px 20px;
      border: none;
      border-radius: 5px;
      cursor: pointer;
    }

    .add-btn:hover {
      background: darkgreen;
    }

    /* Delete Button */
    .delete-btn {
      background: red;
      color: white;
      border: none;
      padding: 5px 10px;
      border-radius: 5px;
      margin-left: 10px;
      cursor: pointer;
    }

    .delete-btn:hover {
      background: darkred;
    }

    ul {
      list-style: none;
      padding: 0;
    }

    li {
      background: #f2f2f2;
      margin: 10px;
      padding: 10px;
      border-radius: 5px;
      color: black;
      display: flex;
      justify-content: space-between;
      align-items: center;
    }

    .credit {
      color: green;
      font-weight: bold;
    }

    .debit {
      color: red;
      font-weight: bold;
    }
  </style>
</head>

<body>

  <!-- Branding -->
  <div class="brand">🚀 BCReddy DevOps 🚀</div>
  <div class="app-title">💰 Money Transactions App</div>

  <div class="container">

    <input id="title" placeholder="Title">
    <input id="amount" placeholder="Amount">
    <select id="type">
      <option value="credit">Credit</option>
      <option value="debit">Debit</option>
    </select>

    <br><br>
    <button class="add-btn" onclick="addTransaction()">➕ Add Transaction</button>

    <h2>📊 Transactions</h2>
    <ul id="list"></ul>

  </div>

<script>
const API = "/api";

function load() {
  fetch(API + "/transactions")
    .then(res => res.json())
    .then(data => {
      let list = document.getElementById("list");
      list.innerHTML = "";

      data.forEach(t => {
        list.innerHTML += `
          <li>
            <span>
              ${t.title} - ${t.amount}
              <span class="${t.type}">(${t.type})</span>
            </span>
            <button class="delete-btn" onclick="del(${t.id})">❌ Delete</button>
          </li>`;
      });
    });
}

function addTransaction() {
  fetch(API + "/transactions", {
    method: "POST",
    headers: {"Content-Type": "application/json"},
    body: JSON.stringify({
      title: document.getElementById("title").value,
      amount: document.getElementById("amount").value,
      type: document.getElementById("type").value
    })
  }).then(load);
}

function del(id) {
  fetch(API + "/transactions/" + id, {
    method: "DELETE"
  }).then(load);
}

load();
</script>

</body>
</html>

```

## Nginx Reverse Proxy Config ##

```
vim /etc/nginx/default.d/money.conf
```

```
proxy_http_version 1.1;

location /api/ {
    proxy_pass http://<BACKEND-Nodejs-private-IP>:8080/;
}

location /health {
  stub_status on;
  access_log off;
}
```

```
systemctl restart nginx
```

## Check from the browser ##
```
http://<nginx-public-ip>:80
```

From frontend server:
```
curl http://<BACKEND-IP>:8080/transactions
```

Test Through Nginx
```
curl http://localhost/api/transactions
curl http://localhost:8080/transactions
curl -X DELETE http://localhost:8080/transactions/1
```




