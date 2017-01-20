---
title: Huginn 的 Apache 配置
layout: docs
permalink: /docs/Huginn的Apache配置.html
prevPage: 在Ubuntu-Debian上安装Huginn.html
nextPage: 为什么要在docker上运行Huginn.html
---

```
# Make sure mod_ssl, mod_rewrite, mod_headers, mod_proxy,
# mod_proxy_http and mod_proxy_balancer are enabled
 
<VirtualHost *:80>
    ServerName huginn.example.org
    RedirectPermanent / https://huginn.example.org/
</VirtualHost>
<VirtualHost *:443>
    ServerName huginn.example.org
    DocumentRoot /path/to/huginn/public
 
    RewriteEngine On
 
    RewriteCond %{DOCUMENT_ROOT}/%{REQUEST_FILENAME} !-f
    RewriteRule ^/(.*)$ balancer://upstream%{REQUEST_URI} [P,QSA,L]
 
    <Proxy balancer://upstream>
        BalancerMember http://127.0.0.1:3000
    </Proxy>
 
    ProxyRequests Off
    ProxyVia On
    ProxyPreserveHost On
    RequestHeader set X_FORWARDED_PROTO https
 
    <Proxy *>
        Order allow,deny
        Allow from all
    </Proxy>
 
    <Directory /path/to/huginn/public>
        Allow from all
        AllowOverride all
        Options -MultiViews
    </Directory>
 
    SSLEngine On
    SSLCertificateFile /path/to/cert
    SSLCertificateKeyFile /path/to/private_key
    # maybe not needed, need for example for startssl to point to a local
    # copy of http://www.startssl.com/certs/sub.class1.server.ca.pem
    SSLCertificateChainFile /path/to/chain_file
</VirtualHost>
```

重要提示:

在 Apache 2.4 中，.htaccess 和 VirtualHost 设置需要做一些改动，将 Allow from 和 Deny from 改为 Require all granted 和 Require all denied，如下所示：

从

```
    <Proxy *>
        Order allow,deny
        Allow from all
    </Proxy>

    <Directory /path/to/huginn/public>
        Allow from all
        AllowOverride all
        Options -MultiViews
    </Directory>
```

改为

```
    <Proxy *>
         Require all granted
    </Proxy>

    <Directory /path/to/huginn/public>
        Require all granted
        AllowOverride all
        Options -MultiViews
    </Directory>
```

更多的信息可以查看这个网页: http://tecadmin.net/authz-core-error-client-denied-by-server-configuration/

> 本文由 [Huginn 中文网](http://huginn.cn) 翻译，已经获得项目作者授权，项目原文访问 [Apache Huginn configuration](https://github.com/cantino/huginn/wiki/Apache-Huginn-configuration)


