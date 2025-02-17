## Start the Containers
Run the following command in the gitlab-docker folder to start all the containers:

    docker-compose up -d

## nginx default.conf
    server {
        listen 443 ssl;
        server_name gitlab.local;
        ssl_certificate /etc/nginx/certs/gitlab.local.crt;
        ssl_certificate_key /etc/nginx/certs/gitlab.local.key;
        location / {
            proxy_pass https://gitlab:443;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }
