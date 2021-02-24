# vscode-podman-ubi8
Vscode server using podman compose


Intro

Code can be used to build vscode-server docker image using ubi8/ubi-init base image. 

Sample docker-compose can be used spin the vscode-server container using `podman-compose`.


Commands

1. For building images

```
podman build -f dockerfile -t vscode-server-ssl
```

2. For spinning container

```
podman -f docker-compose -d
```

NOTES:

For the certicates we have used letsencrypt `certbot` to generate certificate. The certificates can be generated on the host and can be mounted as volumes in the container.

```
volumes:
     - '/opt/vscode-server/private:/etc/pki/nginx/private:Z'
     - '/opt/vscode-server/certs:/etc/pki/nginx:Z'
``` 
