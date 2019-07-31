
> [中文文档](<https://github.com/ddssingsong/webrtc_server/blob/master/README-zh.md>)

## Override 

Signaling server based on webrtc, including browser-side display


## Getting started

1. The project relies on Node.js and npm
2. Deploy turn and stun services : https://github.com/coturn/coturn.git

then 
```shell
git clone https://github.com/ddssingsong/webrtc_server.git
cd webrtc_server/
npm install 

# start
node server.js  

#You can see the results below

# Server running at http://0.0.0.0:3000/

```

## Publishing

If you want to deploy to the server, you need to configure the nginx agent.

```angular2
 #代理https
 upstream web {
    server 0.0.0.0:3000;      
        }
 #代理websocket
 upstream websocket {
    server 0.0.0.0:3000;   
        }
        
 server { 
    listen       443; 
    server_name  localhost;
	ssl          on;

    ssl_certificate     /cert/cert.crt;#配置证书
    ssl_certificate_key  /cert/cert.key;#配置密钥

	ssl_session_cache    shared:SSL:1m;
    ssl_session_timeout  50m;
	ssl_protocols TLSv1 TLSv1.1 TLSv1.2 SSLv2 SSLv3;
    ssl_ciphers  HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers  on;

    #charset koi8-r;
    #access_log  logs/host.access.log  main;
    
  #wss 反向代理  
  location /wss {
     proxy_pass http://websocket/; # 代理到上面的地址去
     proxy_read_timeout 60s;
     proxy_set_header Host $host;
     proxy_set_header X-Real_IP $remote_addr;
     proxy_set_header X-Forwarded-for $remote_addr;
     proxy_set_header Upgrade $http_upgrade;
     proxy_set_header Connection 'Upgrade';	
  }
  #https 反向代理
  location / {
     proxy_pass         http://web/;
     proxy_set_header   Host             $host;
     proxy_set_header   X-Real-IP        $remote_addr;
     proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
  }
}


```