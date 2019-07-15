## 使用自签发的CA签发子证书

### 生成 CA

- 创建目录

```bash
export TLS_DIR=/etc/kolla/tls
mkdir -p ${TLS_DIR}/private ${TLS_DIR}/certs
```



- 生成 CA 证书私钥

```shell
openssl genrsa -out ${TLS_DIR}/private/cakey.pem
```



- 生成 CA 证书签发申请文件

```bash
openssl req -subj "/C=CN/ST=Shanghai/L=Shanghai/O=server/CN=server" -new -key ${TLS_DIR}/private/cakey.pem -out ${TLS_DIR}/private/ca.csr
```



- 自签发 CA 证书

```bash
openssl x509 -req -days 36500 -extensions v3_ca -signkey ${TLS_DIR}/private/cakey.pem -in ${TLS_DIR}/private/ca.csr -out ${TLS_DIR}/certs/ca.crt
```



### 用 CA 根证书签发子证书

- 设置 commonName(FQDN)

```bash
export COMMONNAME=example.com
```



- 生成子证书私钥

```bash
openssl genrsa -out ${TLS_DIR}/private/${COMMONNAME}.pem
```



- 生成子证书签发申请文件

```bash
openssl req -subj "/C=CN/ST=Shanghai/L=Shanghai/O=server/CN=${COMMONNAME}" -new -key ${TLS_DIR}/private/${COMMONNAME}.pem -out ${TLS_DIR}/private/${COMMONNAME}.csr
```



- 用自签发的 CA 根证书签发子证书

```bash
openssl x509 -req -days 36500 -extensions v3_req -CA ${TLS_DIR}/certs/ca.crt -CAkey ${TLS_DIR}/private/cakey.pem -set_serial 01 -in ${TLS_DIR}/private/${COMMONNAME}.csr -out ${TLS_DIR}/certs/${COMMONNAME}.crt
```



- 查看证书内容

```bash
openssl x509 -in ${TLS_DIR}/certs/${COMMONNAME}.crt -text -noout
```



### 生成脚本

```bash
#!/bin/bash

# Following environment variables can be overwritten.
# TLS_DIR: The location where the certificate stored.
# COMMONNAME: FQDN or IP address.

TLS_DIR=${TLS_DIR:-/etc/kolla/tls}
COMMONNAME=${COMMONNAME:-example.com}

# Create directories
mkdir -p ${TLS_DIR}/private ${TLS_DIR}/certs

# Generate CA
openssl genrsa -out ${TLS_DIR}/private/cakey.pem
openssl req -new \
    -subj "/C=CN/ST=Shanghai/L=Shanghai/O=server/CN=CAserver" \
    -key ${TLS_DIR}/private/cakey.pem \
    -out ${TLS_DIR}/private/ca.csr
openssl x509 -req \
    -days 36500 \
    -extensions v3_ca \
    -signkey ${TLS_DIR}/private/cakey.pem \
    -in ${TLS_DIR}/private/ca.csr \
    -out ${TLS_DIR}/certs/ca.crt

# Generate certificates(leaf)
openssl genrsa -out ${TLS_DIR}/private/${COMMONNAME}.pem
openssl req -new \
    -subj "/C=CN/ST=Shanghai/L=Shanghai/O=server/CN=${COMMONNAME}" \
    -key ${TLS_DIR}/private/${COMMONNAME}.pem \
    -out ${TLS_DIR}/private/${COMMONNAME}.csr
openssl x509 -req \
    -days 36500 \
    -extensions v3_req \
    -CA ${TLS_DIR}/certs/ca.crt \
    -CAkey ${TLS_DIR}/private/cakey.pem \
    -set_serial 01 \
    -in ${TLS_DIR}/private/${COMMONNAME}.csr \
    -out ${TLS_DIR}/certs/${COMMONNAME}.crt
```