# 准备工作
- 你需要有一个自己的域名，会正确的设置 `DNS解析` 并打开 `CDN代理`
- 你需要有你域名下的 `SSL证书` ,如果没有可以去 **[这个网站][1]** 申请
# 配置环境
纯净的 Debian9 系统
# 配置内容
- 安装V2ray
```
cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
bash <(curl -L -s https://install.direct/go.sh)
```
- 安装 Nginx
```
apt update
apt install nginx
```
- 粘贴你的证书
```
cd /
mkdir key
vim /key/c.pem
```
- 粘贴你的密钥
```
vim /key/k.key
```
- 写入V2ray配置信息
```
cat > /etc/v2ray/config.json <<EOF
{
  "log": {
    "access": "/var/log/v2ray/access.log",
    "error": "/var/log/v2ray/error.log",
    "loglevel": "warning"
  },
  "inbounds": [
    {
      "port": 10000,
      "listen":"127.0.0.1",
      "protocol": "vmess",
      "settings": {
        "clients": [
          {
            "id": "b831381d-6324-4d53-ad4f-8cda48b30811",   //自行生成
            "alterId": 64     //自行更改
          }
        ]
      },
      "streamSettings": {
        "network": "ws",
        "wsSettings": {
        "path": "/ray"       //改为任意路径
        }
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "freedom",
      "settings": {}
    }
  ],
}
EOF
```
- 写入Nginx配置
```
cat > /etc/nginx/conf.d/v2ray.conf <<EOF
server {
    listen 443 ssl;
    ssl on;                                                         
    ssl_certificate       /key/c.pem;  
    ssl_certificate_key   /key/k.key;
    ssl_protocols         TLSv1 TLSv1.1 TLSv1.2;                    
    ssl_ciphers           HIGH:!aNULL:!MD5;

    listen 80;
    server_name  yourdomain.com;   //改为你的域名
    location / {
        proxy_pass https://proxy.com;  //改为你想伪装的网址
        proxy_redirect     off;
        proxy_connect_timeout      75; 
        proxy_send_timeout         90; 
        proxy_read_timeout         90; 
        proxy_buffer_size          4k; 
        proxy_buffers              4 32k; 
        proxy_busy_buffers_size    64k; 
        proxy_temp_file_write_size 64k; 
     }

    location /ray {                  //改为与上方设置相同的路径
        proxy_redirect off;
        proxy_pass http://127.0.0.1:10000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $http_host;
        proxy_read_timeout 300s;
    }
}
EOF
```
- 运行 V2ray
```
chmod 777 /key/*
nginx -s reload
service v2ray restart
```
# 可选配置
- 使用BBR加速：  
```
cd /usr/src && wget -N --no-check-certificate "https://raw.githubusercontent.com/charlieethan/bbr/master/tcp.sh" && chmod +x tcp.sh && ./tcp.sh
```
- 先选择内核安装，重启后安装加速模块  
```
cd /usr/src && ./tcp.sh
```
# 客户端配置

![2][2]

**yourdomain**填你的域名 ，**id**和**alterId**填你上面设置的  
**Path**填上面设置的路径 ，其余部分照抄即可
# 客户端
Windows系统: [点击下载](https://github.com/charlieethan/firewall-proxy/releases/download/V3.15.2/v2rayN.zip)

Android系统: [点击下载](https://github.com/charlieethan/firewall-proxy/releases/download/V3.15.2/v2rayNG-1.2.5.apk) 

  [1]: https://freessl.cn/
  [2]: https://github.com/charlieethan/firewall-proxy/blob/master/photos/176692878.jpg
