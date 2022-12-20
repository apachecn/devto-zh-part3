# MikroTik 上的 OpenVPN 服务器和证书管理

> 原文：<https://dev.to/garyker/openvpn-server-and-certificate-management-on-mikrotik-54b3>

# 设置 OpenVPN 服务器并生成证书

# 改变下面的变量并粘贴脚本

# 进入 MikroTik 终端窗口。

:global CN[/system identity get name]
:global COUNTRY " UA "
:global STATE " KV "
:global LOC " Kyiv "
:global ORG "我的组织"
:global OU " "
:global key size " 2048 "

## 功能

:global wait sec do = {:return($ KEYSIZE * 10/1024)}

## 生成一个 CA 证书

/certificate
add name = ca-template COUNTRY = " $ COUNTRY " STATE = " $ locality " \
organization = " $ ORG " unit = " $ OU " common-name = " $ CN " key-size = " $ KEYSIZE " \
days-valid = 3650 key-usage = CRL-sign，key-cert-sign
sign ca-template ca-CRL-host = 127 . 0 . 0 . 1 name = " $ CN "
:delay[$ wait sec]

## 生成服务器证书

/certificate
add name = server-template COUNTRY = " $ COUNTRY " STATE = " $ STATE " locality = " $ LOC " \
organization = " $ ORG " unit = " $ OU " common-name = " server @ $ CN " key-size = " $ KEYSIZE " \
days-valid = 3650 key-usage = digital-signature，key-encryption，TLS-server
sign server-template ca = " $ CN " name = " server @ $ CN "
:delay[$ wait sec]

## 创建客户端模板

/certificate
add name = client-template COUNTRY = " $ COUNTRY " STATE = " $ STATE " locality = " $ LOC " \
organization = " $ ORG " unit = " $ OU " common-name = " client " \
key-size = " $ KEYSIZE " days-valid = 3650 key-usage = TLS-client

## 创建 IP 池

/ip 池
添加名称= VPN-池范围= 192 . 168 . 252 . 128-192 . 168 . 252 . 224

## 添加 VPN 配置文件

/PPP PROFILE
add DNS-server = 192 . 168 . 252 . 1 local-address = 192 . 168 . 252 . 1 name = VPN-PROFILE \
remote-address = VPN-POOL use-encryption = yes

## 设置 OpenVPN 服务器

/interface ovpn-server server
set auth = sha1 certificate = " server @ $ CN " cipher = AES 128，aes192，AES 256 \
default-PROFILE = VPN-PROFILE enabled = yes require-client-certificate = yes

## 添加防火墙规则

/ip 防火墙过滤器
add chain = input dst-port = 1194 protocol = TCP comment = " Allow OpenVPN "

添加新用户

# 添加新用户并生成/导出证书

# 改变下面的变量并粘贴脚本

# 进入 MikroTik 终端窗口。

:全局 CN[/系统身份获取名称]
:全局用户名“用户”
:全局密码“密码”

## 添加一个用户

/PPP secret
add name = $ USERNAME PASSWORD = $ PASSWORD PROFILE = VPN-PROFILE service = ovpn

## 生成客户端证书

/certificate
add name = client-template-to-issue copy-from = " client-template " \
common-name = " $ USERNAME @ $ CN "
sign client-template-to-issue ca = " $ CN " name = " $ USERNAME @ $ CN "
:延迟 20

## 导出 CA、客户端证书和私钥

/certificate
export-certificate " $ CN " export-pass phrase = " "
export-certificate " $ USERNAME @ $ CN " export-pass phrase = " $ PASSWORD "

设置 OpenVPN 客户端

从 MikroTik 复制导出的证书

sftp admin @ mikro tik _ IP:cert _ export _ *

此外，您还可以从 web 界面下载证书。为此，请转到 WebFig →文件。

创建 user.auth 文件

auth.cfg 文件包含您的用户名/密码组合。第一行必须是用户名，第二行必须是密码。
用户
密码

创建像 username . ovpn:
client
dev tun
proto TCP-client
remote mikro tik _ IP 1194
no bind
persist-key
persist-tun
cipher AES-256-CBC
auth SHA1
pull
动词 2
静音 3

# 用用户名和密码创建一个文件“user.auth”

# 猫<T3】EOF>user . auth

# 用户

# 密码

# EOF

验证用户-传递用户

# 从 MikroTik 复制证书并更改

# 如果需要，下面的文件名

ca cert _ export _ mikro tik . CRT
cert[cert _ export _ user @ mikro tik . CRT](mailto:cert_export_user@MikroTik.crt)T3】key[cert _ export _ user @ mikro tik . key](mailto:cert_export_user@MikroTik.key)

# 向 MikroTik 后面的网络添加路由

# 路由 192.168.10.0 255.255.255.0

尝试连接
sudo openvpn USERNAME.ovpn

解密私钥避免密码询问
OpenSSL RSA-passin pass:pass-in[cert _ export _ user @ mikro tik . key](mailto:cert_export_user@MikroTik.key)-out[cert _ export _ user @ mikro tik . key](mailto:cert_export_user@MikroTik.key)

删除用户并撤销其证书

# 删除用户并撤销其证书

# 改变下面的变量并粘贴脚本

# 进入 MikroTik 终端窗口。

:全局 CN[/系统身份获取名称]
:全局用户名“用户”

## 删除用户

/PPP secret
remove[find name = $ USERNAME PROFILE = VPN-PROFILE]

## 撤销客户端证书

/证书
已颁发-吊销[find name="$USERNAME@$CN"]

恢复 MikroTik 上的 OpenVPN 服务器配置

# 恢复 OpenVPN 配置

/ip pool
删除[find name=VPN-POOL]

/ppp profile
删除[find name=VPN-PROFILE]

/ip 防火墙过滤器
移除[find comment="Allow OpenVPN"]

/ppp secret
删除[find profile=VPN-PROFILE]

/证书

## 手动删除证书