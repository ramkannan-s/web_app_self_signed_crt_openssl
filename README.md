# web_app_self_signed_crt_openssl

# Web Application with Self Signed Certificate
## _Nginx | OPENSSL_

#### Create Ubuntu Machine and Install Nginx

Create a Ubuntu machine in AWS. Perform the following commands to install nginx.

```sh
sudo apt update -y
sudo apt upgrade -y
sudo apt install nginx -y
sudo ufw allow 'Nginx HTTP'
```

Then create a html file in /var/www/ folder
```sh
cd /var/www/
sudo mkdir -p cloudpage.xyz/html
cd cloudpage.xyz/html
```

Add Contents to the HTML Page
```sh
sudo vi index.html
```

Contents :-
> <h1>Hello, India!</h1>
> <p>We have just configured our Nginx web server on Ubuntu Server!</p>

#### Setting up virtual host
To create file in /etc/nginx/sites-available/ directory.

```sh
cd /etc/nginx/sites-available
vi cloudpage.xyz.conf
```

Contents with 8081 port:-

```sh
server {
       listen 8081;
       listen [::]:8081;

       server_name cloudpage.xyz www.cloudpage.xyz;

       root /var/www/cloudpage.xyz/html;
       index index.html;

       location / {
               try_files $uri $uri/ =404;
       }
}
```

Contents with 443 port :
```sh
server {
    listen 443 ssl;
    server_name cloudpage.xyz www.cloudpage.xyz;

  
    root /var/www/cloudpage.xyz/html;
    index index.php index.html index.htm;

    ssl_certificate     /root/cloudpage.crt;
    ssl_certificate_key     /root/cloudpage.key;
    # if you have received ca-certs.pem from Certification Authority
    #ssl_trusted_certificate /etc/nginx/ssl/example.com/ca-certs.pem;
}
```

Explanation :-
- root is a directory where we have placed our .html file. 
- index is used to specify file available when visiting root directory of site. 
- server_name can be anything you want, because you aren’t pointing it to any real domain by now.


Mapping files 
```sh
sudo ln -s /etc/nginx/sites-available/cloudpage.xyz.conf /etc/nginx/sites-enabled/

sudo nginx -t

sudo service nginx restart
```

```sh
curl -v localhost:8081
```

Nginx Configuration File - `/etc/nginx/nginx.conf`

#### OPENSSL
site name - `cloudpage.xyz`

Commands for creating **private key, csr** and **crt** using **openssl**

```sh
openssl version -a
```

*Generating a Private Key and a Certificate Signing Request*
```sh
openssl req -newkey rsa:2048 -nodes -keyout cloudpage.key -out cloudpage.csr -subj "/C=IN/ST=TN/L=Chennai City/O=CloudPage XYZ Company/CN=cloudpage.xyz"
```

*Generate a Self-Signed Certificate from an Existing Private Key and CSR*
```sh
openssl x509 -signkey cloudpage.key -in cloudpage.csr -req -days 365 -out cloudpage.crt -subj "/C=IN/ST=TN/L=Chennai City/O=CloudPage XYZ Company/CN=cloudpage.xyz"
```

*View CSR and CRT Files*
```sh
openssl req -text -noout -verify -in cloudpage.csr
openssl x509 -text -noout -in cloudpage.crt
```

*Verifying a Certificate was Signed by a Certificate Authority*
```sh
openssl verify -verbose -CAfile ca.crt cloudpage.crt
```

*Verify a Private Key Matches a Certificate and CSR*
```sh
openssl rsa -noout -modulus -in cloudpage.key | openssl md5
openssl x509 -noout -modulus -in cloudpage.crt | openssl md5
openssl req -noout -modulus -in cloudpage.csr | openssl md5
```


**Practice, Hello World!**
