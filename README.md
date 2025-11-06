# hy2
Hy2 极简一键脚本

这个一键脚本超级简单。有效语句6行(其中 安装Hysteria2 1行, 安装Hysteria2服务 2行, 生成自签证书 3行) + 配置文件28行(其中你需要修改4行), 其它都是用来检验小白输入错误参数或者搭建条件不满足的。

你如果不放心开源的脚本，你可以自己执行那6行有效语句，再修改配置文件中的4行，也能达到一样的效果。

## 一键执行
```
apt update
apt install -y curl
```

```
bash <(curl -L https://github.com/crazypeace/hy2/raw/main/install.sh)
```

# Uninstall
```
bash <(curl -fsSL https://get.hy2.sh/) –remove
```

## 带参数执行方式
```
bash <(curl -L https://github.com/crazypeace/hy2/raw/main/install.sh) <netstack> <port> <domain> <password>
```
如
```
bash <(curl -L https://github.com/crazypeace/hy2/raw/main/install.sh) 4 2096 bing.com d3b27d90-507d-30c0-93db-42982a5a33a7
```


## 手搓步骤如下

官方脚本安装 Hy2  
```
bash <(curl -fsSL https://get.hy2.sh/)
```

自签证书  
1. 安装 openssl
```
apt install -y openssl
```   
2. 建个目录用来存放自签证书  
   当然可以是任何你自己喜欢的目录
```
mkdir -p /etc/ssl/private/
```
3. 生成自签证书.crt .key文件  
   这里是自签 bing.com 当然可以是任何你自己喜欢的域名
```
openssl req -x509 -nodes -newkey ec:<(openssl ecparam -name prime256v1) -keyout "/etc/ssl/private/bing.com.key" -out "/etc/ssl/private/bing.com.crt" -subj "/CN=bing.com" -days 36500
```   
4. 给目录和证书设置权限  
   这里粗暴了一点, 直接设置的777. 你想精细化呢, 就设置给官方脚本里service的用户.
```
chmod -R 777 "/etc/ssl/private"
```
5. 修改 /etc/hysteria/config.yaml  
   这个配置文件的位置是官方安装脚本设置的
```
listen: :54321          # HY2工作端口 你自己修改

tls:
  cert: /etc/ssl/private/bing.com.crt     # 证书文件路径
  key: /etc/ssl/private/bing.com.key      # 证书文件路径

auth:
  type: password
  password: *************     # HY2密码 你自己修改

ignoreClientBandwidth: true
```

启用 service  
```
systemctl enable hysteria-server; systemctl start hysteria-server
```
