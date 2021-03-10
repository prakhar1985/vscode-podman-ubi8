FROM registry.access.redhat.com/ubi8/ubi-init:latest AS vscode-server-ssl-devops-ssh

RUN yum install -y \
    wget.x86_64 \
    https://github.com/cdr/code-server/releases/download/v3.9.0/code-server-3.9.0-amd64.rpm \
    nginx \
    sudo \
    openssh-clients



RUN echo -e "\
[Unit]\n\
Description=VS Code Server IDE\n\
After=network.target\n\
 \n\
[Service]\n\
Type=simple\n\
Restart=on-failure\n\
RestartSec=10\n\
Environment="PASSWORD=redhat123"\n\
 \n\
ExecStart=/bin/code-server\n\
ExecStop=/bin/kill -s QUIT $MAINPID\n\
 \n\
[Install]\n\
WantedBy=multi-user.target" > /etc/systemd/system/vscode-server.service


RUN echo -e "\
user nginx;\n\
worker_processes auto;\n\
error_log /var/log/nginx/error.log;\n\
pid /run/nginx.pid;\n\
include /usr/share/nginx/modules/*.conf;\n\
events {\n\
    worker_connections 1024;\n\
}\n\
 \n\
http {\n\
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '\n\
                      '$status $body_bytes_sent "$http_referer" '\n\
                      '"$http_user_agent" "$http_x_forwarded_for"';\n\
    access_log  /var/log/nginx/access.log  main;\n\
 \n\
    sendfile            on;\n\
    tcp_nopush          on;\n\
    tcp_nodelay         on;\n\
    keepalive_timeout   65;\n\
    types_hash_max_size 2048;\n\
 \n\
    include             /etc/nginx/mime.types;\n\
 \n\
    default_type        application/octet-stream;\n\
    include /etc/nginx/conf.d/*.conf;\n\
    server_names_hash_bucket_size 256;\n\
    add_header X-Frame-Options sameorigin always;\n\
 \n\
    server {\n\
        listen       80 default_server;\n\
        listen       [::]:80 default_server;\n\
        listen       443 ssl http2 default_server;\n\
        listen       [::]:443 ssl http2 default_server;\n\
        server_name  _;\n\
        location / {\n\
          proxy_pass http://127.0.0.1:8080;\n\
          proxy_set_header Host \$host;\n\
          proxy_set_header Upgrade \$http_upgrade;\n\
          proxy_set_header Connection upgrade;\n\
          proxy_set_header Accept-Encoding gzip;\n\
        }\n\
       ssl_certificate_key /etc/pki/nginx/private/server.key;\n\
       ssl_certificate /etc/pki/nginx/server.crt;\n\
    }\n\
}" > /etc/nginx/nginx.conf


RUN systemctl enable vscode-server; systemctl enable nginx
EXPOSE 8080


RUN useradd -s /bin/bash -u 1002 -g 0 devops; echo "r3dh4t1!" | passwd --stdin devops

RUN mkdir -p /home/devops/.local/share/code-server/User/
RUN echo -e "\
{\n\
    "git.ignoreLegacyWarning": true,\n\
    "terminal.integrated.experimentalRefreshOnResume": true,\n\
    "window.menuBarVisibility": "visible",\n\
    "git.enableSmartCommit": true,\n\
    "workbench.tips.enabled": false,\n\
    "workbench.startupEditor": "readme",\n\
    "telemetry.enableTelemetry": false,\n\
    "search.smartCase": true,\n\
    "git.confirmSync": false\n\
}" > /home/devops/.local/share/code-server/User/settings.json

RUN code-server --install-extension formulahendry.terminal
RUN code-server --install-extension tyriar.terminal-tabs
RUN code-server --install-extension tyriar.vscode-terminal-here
RUN code-server --install-extension rupisaini.vscode-ansible-linter
RUN code-server --install-extension redhat.vscode-yaml




CMD [ "/sbin/init" ]
