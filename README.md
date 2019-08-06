# OpenVPN for Docker

**the original project - [jpetazzo/dockvpn](https://github.com/jpetazzo/dockvpn)** and it has its own [automatic build on dockerhub](https://hub.docker.com/r/jpetazzo/dockvpn/). 

 
Quick instructions:

```bash
docker build -t vpn https://github.com/cheshaneko/dockvpn.git
CID=$(docker run -d --restart=always --privileged -p 1194:1194/udp -p 443:443/tcp vpn)
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
