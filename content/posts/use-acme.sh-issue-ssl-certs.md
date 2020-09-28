---
title: "使用acme.sh进行泛域名和多域名的Let's Encrypt证书签发"
date: 2018-04-30T11:17:18+08:00
draft: false
---
# 安装acme.sh

## GitHub地址

```
https://github.com/Neilpang/acme.sh
```

## 安装

`sudo apt update`
`sudo apt install curl`
`curl https://get.acme.sh | sh`
`source ~/.bashrc`
`acme.sh -h`
有帮助输出便是安装成功

# 签发证书

这里是签发通配符证书，所以不能像以前使用http验证法了
用dns验证法当然要用api啦
自己修改不仅累死还会忘

## 使用api

我这使用了cloudflare的api
`export CF_Key="233"`
`export CF_Email="233@1.com"`
key填cloudflare的key
Email填账号Email
如果你使用dnspod
`export DP_Id="1234"`
`export DP_Key="sADDsdasdgdsf"`

## 开始签发

不仅支持san还支持泛域名
可以这样写
`acme.sh --dns dns_cf --issue -d a.com -d \*.a.com -d a.me -d \*.a.me -k ec-256`
上面的命令就签发了以下四个域名的ecc证书，在一张证书里。
`a.com`
`*.a.com`
`a.me`
`*.a.me`
然后acme.sh就会根据api修改你的dns记录
还可以签发rsa证书使网站支持ecc+rsa双证书
`acme.sh --dns dns_cf --issue -d a.com -d \*.a.com -d a.me -d \*.a.me`
输出如下
`Your cert is in /home/srotiy/.acme.sh/*.a.com/*.a.com.cer`
`Your cert key is in /home/srotiy/.acme.sh/*.a.com/*.a.com.key`
`The intermediate CA cert is in /home/srotiy/.acme.sh/*.a.com/ca.cer`
`And the full chain certs is there: /home/srotiy/.acme.sh/*.a.com/fullchain.cer`
所有关于该证书的文件就会存在以证书命名的文件夹中
如果是ecc证书，文件夹会看起来像这样
`Your cert is in /home/srotiy/.acme.sh/*.a.com_ecc/*.a.com.cer`
`Your cert key is in /home/srotiy/.acme.sh/*.a.com_ecc/*.a.com.key`
`The intermediate CA cert is in /home/srotiy/.acme.sh/*.a.com_ecc/ca.cer`
`And the full chain certs is there: /home/srotiy/.acme.sh/*.a.com_ecc/fullchain.cer`
*.a.com.cer是证书文件
*.a.com.key是密钥
ca.cer是根证书和中级证书
fullchain.cer是已经带有根证书和中级证书的全部证书

# 部署证书

如果部署双证书可以这样写nginx的配置文件

```nginx
ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
	ssl_ciphers 'ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA:ECDHE-RSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-RSA-AES256-SHA256:DHE-RSA-AES256-SHA:ECDHE-ECDSA-DES-CBC3-SHA:ECDHE-RSA-DES-CBC3-SHA:EDH-RSA-DES-CBC3-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHA:AES256-SHA:DES-CBC3-SHA:!DSS';
	ssl_prefer_server_ciphers on;
```

然后配置ecc的证书

```nginx
ssl_certificate /home/srotiy/.acme.sh/*.a.com_ecc/fullchain.cer;
ssl_certificate /home/srotiy/.acme.sh/*.a.srotiy.com_ecc/*.a.com.key;
```

然后配置rsa证书

```nginx
ssl_certificate /home/srotiy/.acme.sh/*.a.com/fullchain.cer;
ssl_certificate /home/srotiy/.acme.sh/*.a.srotiy.com/*.a.com.key;
```

开启hsts
`add_header Strict-Transport-Security "max-age=63072000; includeSubDomains; preload";`

## 自动续期?

`crontab -l`
如果输出这样一串信息
`10 0 * * * "/home/srotiy/.acme.sh"/acme.sh --cron --home "/home/srotiy/.acme.sh" > /dev/null`
就可以自动续期
此命令是每天的00:10进行续期检测
程序会自动检测到期时间
如果不到一个月
便会续期。
如果时间还长
便会跳过 。