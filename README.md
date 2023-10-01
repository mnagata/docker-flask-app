# docker-flask-app

### 実行方法
Docker環境で実行

```
$ docker-compose up -d
```


### 証明書作成手順
/web/sslの下で作業
```
# 秘密鍵を作成
$ openssl genrsa -out server.key 4096

# csr用設定ファイルを作成
$ cat << _EOF_  > openssl-san.cnf
[req]
distinguished_name = req_distinguished_name
req_extensions = v3_req
prompt = no
   
[req_distinguished_name]
C = JP
ST = Tokyo
L = L
O = O
CN = localhost
 
[v3_req]
keyUsage = nonRepudiation, digitalSignature, keyEncipherment
subjectAltName = @alt_names
 
[alt_names]
DNS.1 = localhost
DNS.2 = 127.0.0.1
DNS.3 = <サーバーIP>
_EOF_

# 公開鍵＋認証局での署名に必要な情報を作成
$ openssl req -new -key server.key -out server.csr -config openssl-san.cnf

# サーバー証明書を作成
$ openssl x509 -in server.csr -days 365 -req -signkey server.key > server.crt
```