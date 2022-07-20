### acme申请单域名ssl证书的脚本
```shell
# xxx.xxx.xxx 更换为需要申请的域名
DOMAIN=xxx.xxx.xxx

echo 开始申请$DOMAIN的域名证书

service nginx stop

~/.acme.sh/acme.sh --issue --standalone -d ${DOMAIN} --keylength ec-256 --force --pre-hook "service nginx stop"  --post-hook  "service nginx start"

mkdir -p /etc/nginx/ssl/${DOMAIN}

~/.acme.sh/acme.sh --install-cert -d ${DOMAIN} \
                --key-file       /etc/nginx/ssl/${DOMAIN}/key.pem  \
                --fullchain-file /etc/nginx/ssl/${DOMAIN}/cert.pem \
                --ecc --force \
                --pre-hook "service nginx stop"  --post-hook  "service nginx start" \
                --server letsencrypt

service nginx restart

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