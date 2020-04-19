# 提要
Shadowsocks有很多个编译版本，例如 go ，python ，C++ 等等。目前还在更新的是 C++ 版本的 Libev 版，本文将介绍 libev 版搭配混淆插件 obfs 的搭建方法 
# 配置环境
纯净的Debian 9 系统
# 配置内容
- 升级系统并安装 Docker
```
apt-get update && apt-get install -y wget vim
wget -qO- get.docker.com | bash
```
- 安装shadowsocks
```
docker pull teddysun/shadowsocks-libev
mkdir -p /etc/shadowsocks-libev
```
- 写入配置
```
cat > /etc/shadowsocks-libev/config.json <<EOF
{
    "server":"0.0.0.0",
    "server_port":9000,     //修改端口
    "password":"password0", //修改密码
    "timeout":300,
    "method":"aes-256-gcm",
    "fast_open":false,
    "nameserver":"8.8.8.8",
    "mode":"tcp_and_udp",
    "plugin":"obfs-server",
    "plugin_opts":"obfs=tls"
}
EOF
```
- 启动服务 （请将`9000`改为你自己设置的端口）
```
docker run -d -p 9000:9000 -p 9000:9000/udp --name ss-libev --restart=always -v /etc/shadowsocks-libev:/etc/shadowsocks-libev teddysun/shadowsocks-libev
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
# 客户端
安卓系统 ： [Shadowsocks下载](https://github.com/charlieethan/firewall-proxy/releases/download/V4.1.10.0/shadowsocks-5.0.6.apk) | [obfs插件下载](https://github.com/charlieethan/firewall-proxy/releases/download/V4.1.10.0/obfs-local-0.0.5.apk)    
Windows系统 ：[点击下载](https://github.com/charlieethan/firewall-proxy/releases/download/V4.1.10.0/Shadowsocks.zip)  
Windows系统 配置如下：  

![2.jpg](https://github.com/charlieethan/firewall-proxy/blob/master/photos/2.jpg)
```
obfs-local
obfs=tls;www.github.com
```
