# OpenVPN for Docker

**the original project - [jpetazzo/dockvpn](https://github.com/jpetazzo/dockvpn)** and it has its own [automatic build on dockerhub](https://hub.docker.com/r/jpetazzo/dockvpn/). 

 
Quick instructions:

```bash
docker build -t vpn https://github.com/cheshaneko/dockvpn.git
CID=$(docker run -d --restart=always --privileged -p 444:444/tcp vpn)
docker run -t -i -p 8080:8080 --volumes-from $CID vpn serveconfig
```

Client certificate creation:

ser_key.pem  ser_ca.pem from vpn server

```bash
openssl dhparam -out dh.pem 1024
openssl genrsa -out key.pem 2048
openssl req -new -key key.pem -out csr.pem -subj /CN=laptop/
openssl x509 -req -in csr.pem -out cert.pem -CAkey ser_key.pem -CA ser_ca.pem -CAcreateserial -days 24855
```

Create dokcer network and route container via vpn

```bash
docker network create \
                --driver=bridge \
                --subnet=172.28.0.0/16 \
                --ip-range=172.28.5.0/24 \
                --gateway=172.28.5.254 \
                br0

docker network connect br0 conteiner_id

nsenter -n -t $(docker inspect --format {{.State.Pid}} conteiner_id) ip route add 10.8.0.0/24 via 172.28.5.0 dev eth1
```

