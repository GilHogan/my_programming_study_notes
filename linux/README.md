### acme申请单域名ssl证书的脚本
```shell
# xxx.xxx.xxx 更换为需要申请的域名
DOMAIN=xxx.xxx.xxx
# 证书颁发机构; acme.sh v3版本使用 Zerossl 作为默认证书颁发机构 (CA); 注意使用ZeroSSL颁发证书需要先注册账户 参考 - https://github.com/acmesh-official/acme.sh/wiki/ZeroSSL.com-CA
# CA列表 参考 - https://github.com/acmesh-official/acme.sh/wiki/Server
CA=letsencrypt

echo 开始申请$DOMAIN的域名证书

# 申请证书；这里指定的所有参数都会被自动记录下来, 并在将来证书自动更新以后, 被再次自动调用。
~/.acme.sh/acme.sh --issue --standalone -d ${DOMAIN} \
                          --server ${CA} --keylength ec-256 --force \
                          --pre-hook "systemctl stop nginx.service"  --post-hook  "systemctl restart nginx.service"

mkdir -p /etc/nginx/ssl/${DOMAIN}

# 创建定时更新证书任务；默认情况下，证书将每60天更新一次；这里指定的所有参数都会被自动记录下来, 并在将来证书自动更新以后, 被再次自动调用。
~/.acme.sh/acme.sh --install-cert -d ${DOMAIN} \
                --key-file       /etc/nginx/ssl/${DOMAIN}/key.pem  \
                --fullchain-file /etc/nginx/ssl/${DOMAIN}/cert.pem \
                --ecc --force \
                --pre-hook "systemctl stop nginx.service"  --post-hook  "systemctl restart nginx.service" \
                --server ${CA}

# 查看已安装证书信息
~/.acme.sh/acme.sh --info -d ${DOMAIN} --ecc

echo 申请结束
```

### nginx ssl 配置示例
```conf
server {

        listen       80;
        listen       443 ssl;
        server_name  xxx.xxx.xxx;
        root         /usr/share/nginx/html;

        ssl_certificate       /etc/nginx/ssl/xxx.xxx.xxx/cert.pem;
        ssl_certificate_key   /etc/nginx/ssl/xxx.xxx.xxx/key.pem;
        ssl_protocols         TLSv1.1 TLSv1.2 TLSv1.3;
        ssl_ciphers           TLS13-AES-256-GCM-SHA384:TLS13-CHACHA20-POLY1305-SHA256:TLS13-AES-128-GCM-SHA256:TLS13-AES-128-CCM-8-SHA256:TLS13-AES-128-CCM-SHA256:EECDH+CHACHA20:EECDH+CHACHA20-draft:EECDH+ECDSA+AES128:EECDH+aRSA+AES128:RSA+AES128:EECDH+ECDSA+AES256:EECDH+aRSA+AES256:RSA+AES256:EECDH+ECDSA+3DES:EECDH+aRSA+3DES:RSA+3DES:!MD5;

        location / {
        }

        error_page 404 /404.html;
            location = /40x.html {
        }

        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }

}
```