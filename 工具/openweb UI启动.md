
# openweb UI

docker run -d --name open-webui -p 3000:8080 -v open-webui:/app/backend/data -e OPENAI_API_BASE_URL=[http://host.docker.internal:8000/v1](http://host.docker.internal:8000/v1) -e ENABLE_RAG=False --restart always ghcr.io/open-webui/open-webui:main

​  


​  

```bash
    docker run -d \
      --name open-webui \
      -p 3000:8080 \
      -v ~/openwebui-custom/static:/app/static:ro \
      -v openwebui-data:/app/backend/data \
      ghcr.io/open-webui/open-webui:main
    
    
      docker run -d \
      --name open-webui \
      -p 3000:8080 \
      -v ~/openwebui-custom/build/index.html:/app/build/index.html \
      -v openwebui-data:/app/backend/data -e ENABLE_RAG=False crpi-f7f20nyvjrv433cv.cn-guangzhou.personal.cr.aliyuncs.com/xinyang123/xinyang-webui:v1
    
      docker run -d \
      --name open-webui-v0.8.2 \
      -p 3001:8080 \
      -v ~/openwebui-custom/build/index-0.8.2.html:/app/build/index.html \
      -v /home/music/openwebui-custom/picture/favicon.png:/app/build/favicon.png \
      -v /home/music/openwebui-custom/picture/favicon.png:/app/build/static/apple-touch-icon.png \
      -v /home/music/openwebui-custom/picture/favicon.png:/app/build/static/favicon-96x96.png \
      -v /home/music/openwebui-custom/picture/favicon.png:/app/build/static/favicon-dark.png \
      -v /home/music/openwebui-custom/picture/favicon.ico:/app/build/static/favicon.ico \
      -v /home/music/openwebui-custom/picture/favicon.png:/app/build/static/favicon.png \
      -v /home/music/openwebui-custom/picture/favicon.svg:/app/build/static/favicon.svg \
      -v /home/music/openwebui-custom/picture/favicon.png:/app/build/static/logo.png \
      -v /home/music/openwebui-custom/picture/favicon.png:/app/build/static/splash-dark.png \
      -v /home/music/openwebui-custom/picture/favicon.png:/app/build/static/splash.png \
      -v /home/music/openwebui-custom/picture/favicon.png:/app/build/static/web-app-manifest-192x192.png \
      -v /home/music/openwebui-custom/picture/favicon.png:/app/build/static/web-app-manifest-512x512.png \
      -v openwebui-data-082:/app/backend/data \
      ghcr.io/open-webui/open-webui:v0.8.2
    
    
    docker run -d \
      --name open-webui-v0.8.2 \
      --restart always \
      -p 127.0.0.1:8081:8080 \
      -v ~/openwebui-custom/build/index-0.8.2.html:/app/build/index.html \
      -v /home/music/openwebui-custom/picture/favicon.png:/app/build/favicon.png \
      -v /home/music/openwebui-custom/picture/favicon.png:/app/build/static/apple-touch-icon.png \
      -v /home/music/openwebui-custom/picture/favicon.png:/app/build/static/favicon-96x96.png \
      -v /home/music/openwebui-custom/picture/favicon.png:/app/build/static/favicon-dark.png \
      -v /home/music/openwebui-custom/picture/favicon.ico:/app/build/static/favicon.ico \
      -v /home/music/openwebui-custom/picture/favicon.png:/app/build/static/favicon.png \
      -v /home/music/openwebui-custom/picture/favicon.svg:/app/build/static/favicon.svg \
      -v /home/music/openwebui-custom/picture/favicon.png:/app/build/static/logo.png \
      -v /home/music/openwebui-custom/picture/favicon.png:/app/build/static/splash-dark.png \
      -v /home/music/openwebui-custom/picture/favicon.png:/app/build/static/splash.png \
      -v /home/music/openwebui-custom/picture/favicon.png:/app/build/static/web-app-manifest-192x192.png \
      -v /home/music/openwebui-custom/picture/favicon.png:/app/build/static/web-app-manifest-512x512.png \
      -v openwebui-data-082:/app/backend/data \
      -e WEBUI_URL=http://10.24.116.22:3001 \
      -e WEBUI_AUTH_TRUSTED_EMAIL_HEADER=X-Internal-User-Email \
      -e WEBUI_AUTH_TRUSTED_NAME_HEADER=X-Internal-User-Name \
      -e WEBUI_AUTH_TRUSTED_GROUPS_HEADER=X-Internal-User-Groups ghcr.io/open-webui/open-webui:v0.8.2
    
        docker run -d \
      --name open-webui-v0.8.2 \
      -p 3001:8080 \
      -v ~/openwebui-custom/build/index.html:/app/build/index.html \
      -v openwebui-data-082:/app/backend/data  ghcr.io/open-webui/open-webui:v0.8.2
    
    公司服务器正常登录的docker启动命令:
      docker run -d \
      --name xinyang-webui-v0.8.2 \
      --restart always \
      -p 3002:8080 \
      -v /home/xinyang/openwebui-custom/build/index-0.8.2.html:/app/build/index.html \
      -v /home/xinyang/openwebui-custom/picture/favicon.png:/app/build/favicon.png \
      -v /home/xinyang/openwebui-custom/picture/favicon.png:/app/build/static/apple-touch-icon.png \
      -v /home/xinyang/openwebui-custom/picture/favicon.png:/app/build/static/favicon-96x96.png \
      -v /home/xinyang/openwebui-custom/picture/favicon.png:/app/build/static/favicon-dark.png \
      -v /home/xinyang/openwebui-custom/picture/favicon.ico:/app/build/static/favicon.ico \
      -v /home/xinyang/openwebui-custom/picture/favicon.png:/app/build/static/favicon.png \
      -v /home/xinyang/openwebui-custom/picture/favicon.svg:/app/build/static/favicon.svg \
      -v /home/xinyang/openwebui-custom/picture/favicon.png:/app/build/static/logo.png \
      -v /home/xinyang/openwebui-custom/picture/favicon.png:/app/build/static/splash-dark.png \
      -v /home/xinyang/openwebui-custom/picture/favicon.png:/app/build/static/splash.png \
      -v /home/xinyang/openwebui-custom/picture/favicon.png:/app/build/static/web-app-manifest-192x192.png \
      -v /home/xinyang/openwebui-custom/picture/favicon.png:/app/build/static/web-app-manifest-512x512.png \
      -v openwebui-data-082:/app/backend/data \
      ghcr.io/open-webui/open-webui:v0.8.2
    
    单点登录的docker启动命令：
    docker run -d \
        --name xinyang-webui-v0.8.2 \
        --restart always \
        -p 127.0.0.1:8081:8080 \
        -v /home/xinyang/openwebui-custom/build/index-0.8.2.html:/app/build/index.html \
        -v /home/xinyang/openwebui-custom/picture/favicon.png:/app/build/favicon.png \
        -v /home/xinyang/openwebui-custom/picture/favicon.png:/app/build/static/apple-touch-icon.png \
        -v /home/xinyang/openwebui-custom/picture/favicon.png:/app/build/static/favicon-96x96.png \
        -v /home/xinyang/openwebui-custom/picture/favicon.png:/app/build/static/favicon-dark.png \
        -v /home/xinyang/openwebui-custom/picture/favicon.ico:/app/build/static/favicon.ico \
        -v /home/xinyang/openwebui-custom/picture/favicon.png:/app/build/static/favicon.png \
        -v /home/xinyang/openwebui-custom/picture/favicon.svg:/app/build/static/favicon.svg \
        -v /home/xinyang/openwebui-custom/picture/favicon.png:/app/build/static/logo.png \
        -v /home/xinyang/openwebui-custom/picture/favicon.png:/app/build/static/splash-dark.png \
        -v /home/xinyang/openwebui-custom/picture/favicon.png:/app/build/static/splash.png \
        -v /home/xinyang/openwebui-custom/picture/favicon.png:/app/build/static/web-app-manifest-192x192.png \
        -v /home/xinyang/openwebui-custom/picture/favicon.png:/app/build/static/web-app-manifest-512x512.png \
        -v openwebui-data-082:/app/backend/data \
        -e WEBUI_URL=http://192.168.8.185:3001 \
        -e WEBUI_AUTH_TRUSTED_EMAIL_HEADER=X-Internal-User-Email \
        -e WEBUI_AUTH_TRUSTED_NAME_HEADER=X-Internal-User-Name \
        -e WEBUI_AUTH_TRUSTED_GROUPS_HEADER=X-Internal-User-Groups \
        -e WEBUI_SECRET_KEY=$(openssl rand -hex 32) \
        -e ENABLE_SIGNUP=true \
        ghcr.io/open-webui/open-webui:v0.8.2
    
```

  


/var/www/html/openwebui-sso/login.html
重启Nginx：sudo nginx -t && sudo systemctl reload nginx

​  

```bash
    docker run -d \
       --name xinyang-webui-v0.8.2 \
       --restart always \
       -p 127.0.0.1:8081:8080 \
       -v /home/xinyang/openwebui-custom/build/index-0.8.2.html:/app/build/index.html \
       -v /home/xinyang/openwebui-custom/picture/favicon.png:/app/build/favicon.png \
       -v /home/xinyang/openwebui-custom/picture/favicon.png:/app/build/static/apple-touch-icon.png \
       -v /home/xinyang/openwebui-custom/picture/favicon.png:/app/build/static/favicon-96x96.png \
       -v /home/xinyang/openwebui-custom/picture/favicon.png:/app/build/static/favicon-dark.png \
       -v /home/xinyang/openwebui-custom/picture/favicon.ico:/app/build/static/favicon.ico \
       -v /home/xinyang/openwebui-custom/picture/favicon.png:/app/build/static/favicon.png \
       -v /home/xinyang/openwebui-custom/picture/favicon.svg:/app/build/static/favicon.svg \
       -v /home/xinyang/openwebui-custom/picture/favicon.png:/app/build/static/logo.png \
       -v /home/xinyang/openwebui-custom/picture/favicon.png:/app/build/static/splash-dark.png \
       -v /home/xinyang/openwebui-custom/picture/favicon.png:/app/build/static/splash.png \
       -v /home/xinyang/openwebui-custom/picture/favicon.png:/app/build/static/web-app-manifest-192x192.png \
       -v /home/xinyang/openwebui-custom/picture/favicon.png:/app/build/static/web-app-manifest-512x512.png \
       -v openwebui-data-082:/app/backend/data \
       -e WEBUI_URL=http://192.168.8.185:3001 \
       -e WEBUI_AUTH_TRUSTED_EMAIL_HEADER=X-Internal-User-Email \
       -e WEBUI_AUTH_TRUSTED_NAME_HEADER=X-Internal-User-Name \
       -e WEBUI_AUTH_TRUSTED_GROUPS_HEADER=X-Internal-User-Groups \
       -e WEBUI_SECRET_KEY=$(openssl rand -hex 32) \
       -e ENABLE_SIGNUP=true \
       ghcr.io/open-webui/open-webui:v0.8.2
```

[http://192.168.8.185:3001/login.html?user_email=**xinyang@xinyang.com**](http://192.168.8.185:3001/login.html?user_email=test@example.com&user_name=testuser)

[ http://192.168.8.185:3001/login.html?user_email=test@test.com](http://192.168.8.185:3001/login.html?user_email=test@example.com&user_name=testuser)

[http://192.168.8.185:3001/login.html?user_email=ljs@chipsun.com](http://192.168.8.185:3001/login.html?user_email=test@example.com&user_name=testuser)

[http://192.168.8.185:3001/?user_email=test@test.com](http://192.168.8.185:3001/?user_email=test@test.com)

![image.png](https://cdn.nlark.com/yuque/0/2026/png/43223830/1772067411581-fa144e2d-1afb-4c52-bd96-8d5772748402.png)

  

```bash
    server {
        listen 3001;
        server_name 192.168.8.185;
    
        allow 192.168.0.0/16;
        allow 127.0.0.1;
        deny all;
    
        access_log /var/log/nginx/openwebui-sso.log sso_format;
    
        location /test {
            default_type text/plain;
            return 200 "Cookie: \$http_cookie\nX_User_Email: \$cookie_X_User_Email\nX_User_Name: \$cookie_X_User_Name\n";
        }
    
        location /ws/socket.io/ {
            proxy_pass http://127.0.0.1:8081;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_read_timeout 600s;
        }
    
        location = /login.html {
            root /var/www/html/openwebui-sso;
            index login.html;
            add_header Cache-Control "no-store, no-cache, must-revalidate";
        }
    
        location /api/v1/auths/signout {
            add_header Set-Cookie "X_User_Name=; Path=/; HttpOnly; SameSite=Lax; Max-Age=0";
            add_header Set-Cookie "X_User_Email=; Path=/; HttpOnly; SameSite=Lax; Max-Age=0";
            add_header Set-Cookie "X_User_Groups=; Path=/; HttpOnly; SameSite=Lax; Max-Age=0";
            proxy_pass http://127.0.0.1:8081;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    
        location / {
            proxy_pass http://127.0.0.1:8081;
    
            proxy_set_header X-Internal-User-Email $cookie_X_User_Email;
            proxy_set_header X-Internal-User-Name $cookie_X_User_Name;
    
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
    
            add_header Access-Control-Allow-Origin * always;
            add_header Access-Control-Allow-Methods "GET, POST, OPTIONS" always;
            add_header Access-Control-Allow-Headers "X-Internal-User-Email, X-Internal-User-Name, Content-Type" always;
    
            if ($request_method = OPTIONS) {
                return 204;
            }
        }
    }
```


  


  


​  


​  
