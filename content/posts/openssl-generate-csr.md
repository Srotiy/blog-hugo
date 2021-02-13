---
title: "OPENSSL生成CSR"
date: 2021-02-13T12:00:00+08:00
draft: false
tags: ["openssl"]
---
```
openssl genrsa -out server.key 2048  
openssl req -new -key server.key -out server.csr -subj "/C=HK/ST=/L=/O=/OU=/CN=domain.com"
```
#### 自签名
```
openssl req -new -x509 -days 3650 -key server.key -out server.crt -subj "/C=/ST=/L=/O=/OU=/CN="  
```
