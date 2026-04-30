# Lab M2.04 - Enable HTTP Access and Configure Web Server

**Repository:** [https://github.com/cloud-engineering-bootcamp/ce-lab-enable-http-access](https://github.com/cloud-engineering-bootcamp/ce-lab-enable-http-access)

**Activity Type:** Individual  
**Estimated Time:** 30-45 minutes

## Learning Objectives

- [ ] Configure security groups to allow HTTP traffic
- [ ] Set up Nginx as a reverse proxy
- [ ] Test traffic flow from internet to application
- [ ] Implement HTTP to HTTPS redirection
- [ ] Verify proper configuration

## Your Task

Configure your EC2 instance to serve a web application publicly:
1. Allow HTTP traffic through security group
2. Install and configure Nginx
3. Deploy a simple Node.js application
4. Configure Nginx to proxy requests
5. Test from internet

**Success:** Public URL serving your application

## Quick Start

```bash
# 1. Update security group - add HTTP (80) from 0.0.0.0/0

# 2. Install Nginx
sudo yum install -y nginx

Output: 
[ec2-user@ip-172-31-38-85 ~]$ sudo yum install -y nginx
Amazon Linux 2023 Kernel Livepatch repository                                                   243 kB/s |  31 kB     00:00    
Dependencies resolved.
================================================================================================================================
 Package                           Architecture         Version                                 Repository                 Size
================================================================================================================================
Installing:
 nginx                             x86_64               1:1.28.3-1.amzn2023.0.1                 amazonlinux                33 k
Installing dependencies:
 generic-logos-httpd               noarch               18.0.0-12.amzn2023.0.3                  amazonlinux                19 k
 gperftools-libs                   x86_64               2.9.1-1.amzn2023.0.3                    amazonlinux               308 k
 libunwind                         x86_64               1.4.0-5.amzn2023.0.3                    amazonlinux                66 k
 nginx-core                        x86_64               1:1.28.3-1.amzn2023.0.1                 amazonlinux               688 k
 nginx-filesystem                  noarch               1:1.28.3-1.amzn2023.0.1                 amazonlinux               9.8 k
 nginx-mimetypes                   noarch               2.1.49-3.amzn2023.0.3                   amazonlinux                21 k

Transaction Summary
================================================================================================================================
Install  7 Packages

Total download size: 1.1 M
Installed size: 3.7 M
Downloading Packages:
(1/7): gperftools-libs-2.9.1-1.amzn2023.0.3.x86_64.rpm                                          8.2 MB/s | 308 kB     00:00    
(2/7): libunwind-1.4.0-5.amzn2023.0.3.x86_64.rpm                                                1.6 MB/s |  66 kB     00:00    
(3/7): generic-logos-httpd-18.0.0-12.amzn2023.0.3.noarch.rpm                                    431 kB/s |  19 kB     00:00    
(4/7): nginx-1.28.3-1.amzn2023.0.1.x86_64.rpm                                                   1.2 MB/s |  33 kB     00:00    
(5/7): nginx-core-1.28.3-1.amzn2023.0.1.x86_64.rpm                                               23 MB/s | 688 kB     00:00    
(6/7): nginx-filesystem-1.28.3-1.amzn2023.0.1.noarch.rpm                                        330 kB/s | 9.8 kB     00:00    
(7/7): nginx-mimetypes-2.1.49-3.amzn2023.0.3.noarch.rpm                                         801 kB/s |  21 kB     00:00    
--------------------------------------------------------------------------------------------------------------------------------
Total                                                                                           8.6 MB/s | 1.1 MB     00:00     
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                                                                        1/1 
  Running scriptlet: nginx-filesystem-1:1.28.3-1.amzn2023.0.1.noarch                                                        1/7 
  Installing       : nginx-filesystem-1:1.28.3-1.amzn2023.0.1.noarch                                                        1/7 
  Installing       : nginx-mimetypes-2.1.49-3.amzn2023.0.3.noarch                                                           2/7 
  Installing       : libunwind-1.4.0-5.amzn2023.0.3.x86_64                                                                  3/7 
  Installing       : gperftools-libs-2.9.1-1.amzn2023.0.3.x86_64                                                            4/7 
  Installing       : nginx-core-1:1.28.3-1.amzn2023.0.1.x86_64                                                              5/7 
  Installing       : generic-logos-httpd-18.0.0-12.amzn2023.0.3.noarch                                                      6/7 
  Installing       : nginx-1:1.28.3-1.amzn2023.0.1.x86_64                                                                   7/7 
  Running scriptlet: nginx-1:1.28.3-1.amzn2023.0.1.x86_64                                                                   7/7 
  Verifying        : generic-logos-httpd-18.0.0-12.amzn2023.0.3.noarch                                                      1/7 
  Verifying        : gperftools-libs-2.9.1-1.amzn2023.0.3.x86_64                                                            2/7 
  Verifying        : libunwind-1.4.0-5.amzn2023.0.3.x86_64                                                                  3/7 
  Verifying        : nginx-1:1.28.3-1.amzn2023.0.1.x86_64                                                                   4/7 
  Verifying        : nginx-core-1:1.28.3-1.amzn2023.0.1.x86_64                                                              5/7 
  Verifying        : nginx-filesystem-1:1.28.3-1.amzn2023.0.1.noarch                                                        6/7 
  Verifying        : nginx-mimetypes-2.1.49-3.amzn2023.0.3.noarch                                                           7/7 

Installed:
  generic-logos-httpd-18.0.0-12.amzn2023.0.3.noarch               gperftools-libs-2.9.1-1.amzn2023.0.3.x86_64                  
  libunwind-1.4.0-5.amzn2023.0.3.x86_64                           nginx-1:1.28.3-1.amzn2023.0.1.x86_64                         
  nginx-core-1:1.28.3-1.amzn2023.0.1.x86_64                       nginx-filesystem-1:1.28.3-1.amzn2023.0.1.noarch              
  nginx-mimetypes-2.1.49-3.amzn2023.0.3.noarch                   

Complete!


# 3. Create simple app
mkdir ~/app && cd ~/app
npm init -y
Output: 
[ec2-user@ip-172-31-38-85 app]$ npm init -y
Wrote to /home/ec2-user/app/package.json:

{
  "name": "app",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "description": ""
}

npm install express
Output: 
[ec2-user@ip-172-31-38-85 app]$ npm install express

added 65 packages, and audited 66 packages in 4s

22 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities


cat > index.js <<'EOF'
const express = require('express');
const app = express();

app.get('/', (req, res) => {
  res.send(`
    <h1>Hello from ${process.env.HOSTNAME}!</h1>
    <p>You've successfully configured HTTP access</p>
  `);
});

app.listen(8080, () => {
  console.log('App running on port 8080');
});
EOF

node index.js &

Output: 
[ec2-user@ip-172-31-38-85 ~]$ cat > index.js <<'EOF'
const express = require('express');
const app = express();
 
app.get('/', (req, res) => {
  res.send(`
    <h1>Hello from ${process.env.HOSTNAME}!</h1>
    <p>You've successfully configured HTTP access</p>
  `);
});
 
app.listen(8080, () => {
  console.log('App running on port 8080');
});
EOF
[ec2-user@ip-172-31-38-85 ~]$ node index.js &
[1] 26685
[ec2-user@ip-172-31-38-85 ~]$ node:internal/modules/cjs/loader:1143
  throw err;
  ^

Error: Cannot find module 'express'
Require stack:
- /home/ec2-user/index.js
    at Module._resolveFilename (node:internal/modules/cjs/loader:1140:15)
    at Module._load (node:internal/modules/cjs/loader:981:27)
    at Module.require (node:internal/modules/cjs/loader:1231:19)
    at require (node:internal/modules/helpers:177:18)
    at Object.<anonymous> (/home/ec2-user/index.js:1:17)
    at Module._compile (node:internal/modules/cjs/loader:1364:14)
    at Module._extensions..js (node:internal/modules/cjs/loader:1422:10)
    at Module.load (node:internal/modules/cjs/loader:1203:32)
    at Module._load (node:internal/modules/cjs/loader:1019:12)
    at Function.executeUserEntryPoint [as runMain] (node:internal/modules/run_main:128:12) {
  code: 'MODULE_NOT_FOUND',
  requireStack: [ '/home/ec2-user/index.js' ]
}

Node.js v18.20.8

# 4. Configure Nginx proxy
sudo tee /etc/nginx/conf.d/app.conf <<EOF
server {
    listen 80;
    location / {
        proxy_pass http://localhost:8080;
    }
}
EOF

sudo systemctl start nginx
Output:
[ec2-user@ip-172-31-38-85 ~]$ cat > index.js <<'EOF'
const express = require('express');
const app = express();
 
app.get('/', (req, res) => {
  res.send(`
    <h1>Hello from ${process.env.HOSTNAME}!</h1>
    <p>You've successfully configured HTTP access</p>
  `);
});
 
app.listen(8080, () => {
  console.log('App running on port 8080');
});
EOF
[ec2-user@ip-172-31-38-85 ~]$ node index.js &
[1] 26685
[ec2-user@ip-172-31-38-85 ~]$ node:internal/modules/cjs/loader:1143
  throw err;
  ^

Error: Cannot find module 'express'
Require stack:
- /home/ec2-user/index.js
    at Module._resolveFilename (node:internal/modules/cjs/loader:1140:15)
    at Module._load (node:internal/modules/cjs/loader:981:27)
    at Module.require (node:internal/modules/cjs/loader:1231:19)
    at require (node:internal/modules/helpers:177:18)
    at Object.<anonymous> (/home/ec2-user/index.js:1:17)
    at Module._compile (node:internal/modules/cjs/loader:1364:14)
    at Module._extensions..js (node:internal/modules/cjs/loader:1422:10)
    at Module.load (node:internal/modules/cjs/loader:1203:32)
    at Module._load (node:internal/modules/cjs/loader:1019:12)
    at Function.executeUserEntryPoint [as runMain] (node:internal/modules/run_main:128:12) {
  code: 'MODULE_NOT_FOUND',
  requireStack: [ '/home/ec2-user/index.js' ]
}

Node.js v18.20.8
[ec2-user@ip-172-31-38-85 ~]$ npm install express

added 65 packages in 1s

22 packages are looking for funding
  run `npm fund` for details
[1]+  Exit 1                  node index.js
[ec2-user@ip-172-31-38-85 ~]$ node index.js &
[1] 26755
[ec2-user@ip-172-31-38-85 ~]$ App running on port 8080
[ec2-user@ip-172-31-38-85 ~]$ sudo tee /etc/nginx/conf.d/app.conf <<EOF
server {
    listen 80;
    location / {
        proxy_pass http://localhost:8080;
    }
}
EOF
server {
    listen 80;
    location / {
        proxy_pass http://localhost:8080;
    }
}
[ec2-user@ip-172-31-38-85 ~]$ sudo systemctl start nginx


# 5. Test
curl http://$(curl -s ifconfig.me)
```

Output: 
[ec2-user@ip-172-31-38-85 ~]$ curl http://$(curl -s ifconfig.me)

    <h1>Hello from ip-172-31-38-85.eu-north-1.compute.internal!</h1>
    <p>You've successfully configured HTTP access</p>

## 📤 What to Submit

**Submission Type:** File Upload (ZIP)

Create a ZIP file named `lab-m2-http-access.zip` containing:

1. **Nginx Configuration Files:**
   - `/etc/nginx/conf.d/app.conf` (your proxy configuration)
   - `/etc/nginx/nginx.conf` (if modified)

2. **Application Files:**
   - `index.js` (your Node.js application)
   - `package.json`

3. **Screenshots** (in `screenshots/` folder):
   - Security group rules showing HTTP port 80 configuration
   - Browser showing your working webpage with public IP
   - Terminal showing Nginx and app running (`systemctl status nginx`)
   - `curl` output testing the connection

4. **Documentation** (`README.md`):
   - Step-by-step process you followed
1. Created EC2 instance
2. Configured security group:
   - Allowed SSH (22)
   - Allowed HTTP (80) from 0.0.0.0/0
3. Installed Node.js and npm
4. Created Express application (index.js)
5. Installed Nginx
6. Configured reverse proxy:
   - Port 80 → 8080
7. Started services:
   - node index.js
   - nginx

   - Your public IP address and test URL
        http://13.48.10.16/
   - Security group ID and rules
        ## Security Group
            - ID: (your security group id)
            - Rules:
            - SSH (22) → 0.0.0.0/0
            - HTTP (80) → 0.0.0.0/0
   - Any troubleshooting you encountered
        - SSH timeout → fixed by updating security group
        - npm not found → installed Node.js
        - express module missing → installed via npm
   - Reflection: Why use Nginx as a reverse proxy?
        Nginx is used as a reverse proxy to:
          - Expose applications on standard HTTP port (80)
          - Improve performance and scalability
          - Add a layer of abstraction between client and backend
          - Enable load balancing and security features



**File Structure:**
```
lab-m2-http-access.zip
├── README.md
├── configs/
│   ├── app.conf
│   └── nginx.conf (if modified)
├── application/
│   ├── index.js
│   └── package.json
└── screenshots/
    ├── 01-security-group-http-rule.png
    ├── 02-browser-working-webpage.png
    ├── 03-services-running.png
    └── 04-curl-test.png
```

