## Prerequisites
Windows 11 with WSL installed: Ensure you have WSL installed and a Linux distribution (e.g., Ubuntu) set up.

Docker Desktop: Install Docker Desktop for Windows and ensure it is configured to use WSL 2.


    mkdir -p ~/gitlab-docker-project/{gitlab,gitlab-runner,nginx,postgresql}
    cd ~/gitlab-docker-project
    mkdir -p {gitlab,gitlab-runner,nginx,postgresql}/{config,logs,data,certs}
    mkdir -p nginx/conf.d


### This will create below folder structure
    gitlab-docker-project/
    ├── gitlab/
    │   ├── config/
    │   ├── logs/
    │   ├── data/
    │   └── certs/
    ├── gitlab-runner/
    │   ├── config/
    │   ├── logs/
    │   ├── data/
    │   └── certs/
    ├── nginx/
    │   ├── conf.d/
    │   ├── config/
    │   ├── logs/
    │   ├── data/
    │   └── certs/    
    └── postgresql/
        ├── config/
        ├── logs/
        ├── data/
        └── certs/
        


## Generate self-signed certificate
As gitlab needs SSL to communicate and download gitlab repositories when you clone them in the docker/wsl

    openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
      -keyout ~/gitlab-docker-project/nginx/certs/gitlab.local.key \
      -out ~/gitlab-docker-project/nginx/certs/gitlab.local.crt


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

## How to register Gitlab runner
    docker exec -it gitlab-runner gitlab-runner register
Also note that the config.toml file for the runner contains the required configuration for runner docker for the jobs in the same network as the main gitlab project
