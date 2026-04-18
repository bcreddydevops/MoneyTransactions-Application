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
<html>
<head>
  <title>Money Transactions</title>
</head>
<body>
  <h1>Money Transactions</h1>

  <input id="title" placeholder="Title">
  <input id="amount" placeholder="Amount">
  <select id="type">
    <option value="credit">Credit</option>
    <option value="debit">Debit</option>
  </select>
  <button onclick="addTransaction()">Add</button>

  <h2>Transactions</h2>
  <ul id="list"></ul>

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
            ${t.title} - ${t.amount} (${t.type})
            <button onclick="del(${t.id})">Delete</button>
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
```




