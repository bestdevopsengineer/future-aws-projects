# Project: AWS EC2 + RDS MySQL + Node.js App
      1.	Create RDS MySQL
      Engine: MySQL
      DB name: myapp-db
      Username: admin
      Password: your password
      Public access: No
      VPC: same VPC as EC2
      Security group: rds-mysql-sg
      
      2.	Create EC2 Security Group
      ec2-app-sg
      SSH     22    Your IP
      HTTP    80    0.0.0.0/0
      HTTPS   443   0.0.0.0/0
      
      3.	Launch EC2
      AMI: Amazon Linux 2023
      Type: t2.micro or t3.micro
      Key pair: create/download .pem
      VPC: same VPC as RDS
      Security group: ec2-app-sg
      
      4.	Allow EC2 to Access RDS
      Go to RDS security group: rds-mysql-sg
      Type: MySQL/Aurora
      Port: 3306
      Source: ec2-app-sg
      
      5.	Connect to EC2 from Windows
      ssh -i my-key.pem ec2-user@EC2_PUBLIC_IP
      
      6.	Install MySQL Client
      sudo dnf install mariadb105 -y
      mysql --version
      
      7.	Connect EC2 to RDS
      mysql -h RDS_ENDPOINT -u admin -p
      
      
      
      8.	Create Database and Table
      
      CREATE DATABASE myapp;
      USE myapp;
      
      CREATE TABLE users (
        id INT AUTO_INCREMENT PRIMARY KEY,
        name VARCHAR(100),
        email VARCHAR(100)
      );
      
      INSERT INTO users (name, email)
      VALUES ('John Doe', 'john@example.com');
      
      SELECT * FROM users;
      
      9.	Install Nginx
      sudo dnf install nginx -y
      sudo systemctl enable nginx
      sudo systemctl start nginx
      
      10.	Install Node.js
      sudo dnf install nodejs npm -y
      node -v
      npm –v
      
      11.	Create Node.js App
      mkdir myapp
      cd myapp
      npm init -y
      npm install express mysql2
      
      vi app.js
      
      const express = require("express");
      const mysql = require("mysql2");
      
      const app = express();
      
      const db = mysql.createConnection({
        host: "YOUR_RDS_ENDPOINT",
        user: "admin",
        password: "YOUR_RDS_PASSWORD",
        database: "myapp"
      });
      
      app.get("/", (req, res) => {
        db.query("SELECT * FROM users", (err, results) => {
          if (err) return res.send("Database error: " + err.message);
          res.json(results);
        });
      });
      
      app.listen(3000, () => {
        console.log("App running on port 3000");
      });
      
      12.	Install PM2
      sudo npm install -g pm2
      pm2 start app.js --name myapp
      pm2 save
      pm2 startup systemd
      
      pm2 save
      
      13.	Configure Nginx Reverse Proxy
      sudo vi  /etc/nginx/nginx.conf
      user nginx;
      worker_processes auto;
      error_log /var/log/nginx/error.log notice;
      pid /run/nginx.pid;
      
      include /usr/share/nginx/modules/*.conf;
      
      events {
          worker_connections 1024;
      }
      
      http {
          log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                          '$status $body_bytes_sent "$http_referer" '
                          '"$http_user_agent" "$http_x_forwarded_for"';
      
          access_log /var/log/nginx/access.log main;
      
          sendfile on;
          tcp_nopush on;
          keepalive_timeout 65;
          types_hash_max_size 4096;
      
          include /etc/nginx/mime.types;
          default_type application/octet-stream;
      
          server {
              listen 80;
              listen [::]:80;
              server_name _;
      
              location / {
                  proxy_pass http://127.0.0.1:3000;
                  proxy_http_version 1.1;
                  proxy_set_header Host $host;
                  proxy_set_header X-Real-IP $remote_addr;
              }
      
              error_page 404 /404.html;
              location = /404.html { }
      
              error_page 500 502 503 504 /50x.html;
              location = /50x.html { }
          }
      }
      
      sudo nginx -t
      sudo systemctl restart nginx


