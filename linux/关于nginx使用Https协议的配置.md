# 关于nginx使用Https协议的配置

```sh
server {
        listen 80;
        server_name www.repository.com;
        # 重定向到https
        rewrite ^/(.*) https://$server_name$request_uri? permanent;    #重定向
}

server {
	# https默认使用443端口
	listen    443;
	server_name www.repository.com;

	# https证书相关的配置
	ssl on; 
	ssl_certificate     ssl/www.repository.com.crt;
	ssl_certificate_key ssl/www.repository.com.key;


	proxy_set_header X-Forwarded-Host $host;
	proxy_set_header X-Forwarded-Server $host;
	proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;


	location / { 
		proxy_pass https://172.21.18.245:8527;
		proxy_connect_timeout 600;
		proxy_read_timeout 600;
	}
}

```

