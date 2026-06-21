# 公司服务器登录信息

向日葵远程

  * 设备码：110 919 3763 密码：c5863t


虚拟机

  * ip:192.168.8.185
  * 用户名：xinyang
  * 密码：123456


# Open WebUI 单点登录完整方案总结

## 一、Docker 容器启动配置
```text
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
        # -e BYPASS_ADMIN_ACCESS_CONTROL=true
        ghcr.io/open-webui/open-webui:v0.8.2
```

**关键环境变量：**

  * ` WEBUI_AUTH_TRUSTED_EMAIL_HEADER=X-Internal-User-Email` \- 信任的邮箱请求头
  * `WEBUI_AUTH_TRUSTED_NAME_HEADER=X-Internal-User-Name` \- 信任的用户名请求头
  * `WEBUI_AUTH_TRUSTED_GROUPS_HEADER=X-Internal-User-Groups` \- 信任的用户组请求头


## 二、Nginx 主配置文件

**文件路径：** `/etc/nginx/nginx.conf`
```text
    user www-data;
    worker_processes auto;
    pid /run/nginx.pid;
    include /etc/nginx/modules-enabled/*.conf;
    
    events {
        worker_connections 768;
    }
    
    http {
        sendfile on;
        tcp_nopush on;
        types_hash_max_size 2048;
    
        include /etc/nginx/mime.types;
        default_type application/octet-stream;
    
        ssl_protocols TLSv1.2 TLSv1.3;
        ssl_prefer_server_ciphers on;
    
        access_log /var/log/nginx/access.log;
        error_log /var/log/nginx/error.log;
    
        gzip on;
    
        map $cookie_X_User_Email $header_user_email {
            default $cookie_X_User_Email;
            "" $arg_user_email;
        }
    
        map $cookie_X_User_Name $header_user_name {
            default $cookie_X_User_Name;
            "" $arg_user_name;
        }
    
        log_format sso_format '$remote_addr - $remote_user [$time_local] "$request" '
            '$status $body_bytes_sent "$http_referer" '
            'cookie: "$http_cookie" '
            'X-Internal-User-Email: $header_user_email '
            'X-Internal-User-Name: $header_user_name';
    
        include /etc/nginx/conf.d/*.conf;
    }
```

## 三、Nginx 站点配置文件

**文件路径：** `/etc/nginx/conf.d/openwebui-sso.conf`
```text
    server {
        listen 3001;
        server_name 192.168.8.185;
    
        allow 192.168.0.0/16;
        allow 127.0.0.1;
        deny all;
    
        access_log /var/log/nginx/openwebui-sso.log sso_format;
    
        # WebSocket 支持
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
    
        # 登录页面
        location = /login.html {
            root /var/www/html/openwebui-sso;
            index login.html;
            add_header Cache-Control "no-store, no-cache, must-revalidate";
        }
    
        # 登出时清除 Cookie
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
    
        # 主路由
        location / {
            proxy_pass http://127.0.0.1:8081;
    
            # 从 Cookie 提取用户信息并转发为请求头
            proxy_set_header X-Internal-User-Email $cookie_X_User_Email;
            proxy_set_header X-Internal-User-Name $cookie_X_User_Name;
    
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
    
            # CORS 配置
            add_header Access-Control-Allow-Origin * always;
            add_header Access-Control-Allow-Methods "GET, POST, OPTIONS" always;
            add_header Access-Control-Allow-Headers "X-Internal-User-Email, X-Internal-User-Name, Content-Type" always;
    
            if ($request_method = OPTIONS) {
                return 204;
            }
        }
    }
```

## 四、登录页面配置

**文件路径：** `/var/www/html/openwebui-sso/login.html`
```text
    
    
    
        
    
    # 正在登录...
    
    
        
    
    
        
    
    
    
```

## 五、使用方法

### 1\. 单点登录访问
```text
    http://192.168.8.185:3001/login.html?user_email=test@test.com
```

或同时指定用户名：
```text
    http://192.168.8.185:3001/login.html?user_email=test@test.com&user;_name=testuser
```

### 2\. 工作流程

  1. 用户访问 `login.html?user_email=xxx@xxx.com`
  2. login.html 从 URL 参数提取邮箱，自动提取用户名（如未指定）
  3. login.html 设置 Cookie：`X_User_Email` 和 `X_User_Name`
  4. 页面跳转到首页 `/`
  5. Nginx 从 Cookie 读取用户信息
  6. Nginx 将 Cookie 值转发为请求头：`X-Internal-User-Email` 和 `X-Internal-User-Name`
  7. Open WebUI 验证 trusted header，自动登录用户


### 3\. 登出

点击登出按钮后，Nginx 会清除 Cookie：

  * `X_User_Name`
  * `X_User_Email`
  * `X_User_Groups`


## 六、重要注意事项

### 1\. Cookie 命名规范

  * **必须使用下划线** ：`X_User_Email`、`X_User_Name`
  * **不能使用连字符** ：`X-User-Email`、`X-User-Name`（Nginx 无法正确解析）


### 2\. 邮箱地址编码

  * **不要使用**` encodeURIComponent` 编码邮箱
  * 保持 `@` 符号原样传递


### 3\. 用户管理

  * Trusted header 模式下，第一个登录的用户自动成为管理员
  * 不会自动创建用户，需要管理员在后台手动创建或导入
  * 用户状态需要管理员手动激活为"已激活"


### 4\. 已知问题

  * 用户 A 登出后，用户 B 登录再登出，用户 A 需要关闭浏览器重新登录
  * 浏览器地址栏会显示明文的用户名和邮箱


### 5\. 重载 Nginx 配置
```text
    sudo nginx -t          # 测试配置
    sudo nginx -s reload    # 重载配置
```

## 七、文件权限
```text
    # Nginx 配置文件
    sudo chown root:root /etc/nginx/nginx.conf
    sudo chmod 644 /etc/nginx/nginx.conf
    
    # 站点配置文件
    sudo chown root:root /etc/nginx/conf.d/openwebui-sso.conf
    sudo chmod 644 /etc/nginx/conf.d/openwebui-sso.conf
    
    # 登录页面
    sudo chown www-data:www-data /var/www/html/openwebui-sso/login.html
    sudo chmod 644 /var/www/html/openwebui-sso/login.html
```

