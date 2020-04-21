# 配置环境
- 纯净的 Debian9 系统
- 默认使用东八区（上海）标准时间

# 配置内容
- 安装V2ray
```bash
cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
bash <(curl -L -s https://install.direct/go.sh)
vim /etc/v2ray/config.json
```
- 生成配置文件     
**进入 https://tools.sprov.xyz/v2ray/ 生成配置文件**  (进入网站需翻墙）           
示例配置如下：   

![01](https://github.com/charlieethan/firewall-proxy/blob/master/photos/3.jpg)

需要更改的部分：1.**id**  2.**alterid**  3.**端口**  4.**伪装**
完成后点击 **导出** 并复制配置文件  
- 粘贴配置   
1. `:wq` 保存并退出  
2. `service v2ray restart` 重启服务

# 客户端
Windows系统: [点击下载](https://github.com/charlieethan/firewall-proxy/releases/download/V3.16/v2rayN.zip)

Android系统: [点击下载](https://github.com/charlieethan/firewall-proxy/releases/download/V3.15.2/v2rayNG-1.2.5.apk) 
