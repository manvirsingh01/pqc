# Complete PQC HTTPS Apache + Node.js Implementation Guide

This document contains all installation, configuration, verification, troubleshooting, and PQC validation commands used to implement Apache + Node.js with TLS 1.3 and X25519MLKEM768.

## Install

```bash
sudo apt update
sudo apt upgrade -y
sudo apt install -y apache2 nodejs npm openssl build-essential cmake git curl wget tshark
```

## Verify Versions

```bash
openssl version
apache2 -v
node -v
npm -v
```

## Node.js Setup

```bash
mkdir ~/pqc-api
cd ~/pqc-api
npm init -y
npm install express
```

server.js:

```javascript
const express = require('express');
const app = express();
app.get('/', (req,res)=>res.send('Post Quantum Protected API'));
app.listen(3000,'127.0.0.1');
```

Run:

```bash
node server.js
curl http://127.0.0.1:3000
```

## Apache Modules

```bash
sudo a2enmod proxy
sudo a2enmod proxy_http
sudo a2enmod rewrite
sudo a2enmod ssl
sudo a2enmod headers
```

## SSL Certificate

```bash
sudo mkdir -p /etc/apache2/ssl
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/apache2/ssl/apache.key -out /etc/apache2/ssl/apache.crt
```

## PQC Check

```bash
openssl list -tls-groups
```

Look for X25519MLKEM768.

## Apache Config

Create /etc/apache2/sites-available/pqc.conf and configure HTTPS reverse proxy with:

- SSLCertificateFile /etc/apache2/ssl/apache.crt
- SSLCertificateKeyFile /etc/apache2/ssl/apache.key
- SSLOpenSSLConfCmd Curves X25519MLKEM768:X25519
- ProxyPass to http://127.0.0.1:3000/

## Enable Site

```bash
sudo a2ensite pqc.conf
sudo a2dissite 000-default.conf
```

## Validate and Start

```bash
sudo apache2ctl configtest
sudo systemctl restart apache2
sudo systemctl enable apache2
sudo systemctl status apache2
```

## Verification

```bash
curl -k https://localhost
openssl s_client -connect localhost:443 -groups X25519MLKEM768 -tls1_3
curl -Ik https://localhost
sudo ss -tulpn | grep -E ':80|:443|:3000'
```

Required proof:

```text
Negotiated TLS1.3 group: X25519MLKEM768
```

## TShark

```bash
sudo tshark -i lo -f "tcp port 443"
```

Open https://localhost in browser.
