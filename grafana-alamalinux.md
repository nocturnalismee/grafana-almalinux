# INSTALL & KONFIGURASI GRAFANA ON  ALMALINUX/RHELL

## Update system

```bash
sudo dnf update -y && sudo dnf upgrade -y
```

## Import the GPG key

```bash
wget -q -O gpg.key https://rpm.grafana.com/gpg.key
sudo rpm --import gpg.key
```
## Buat repository /etc/yum.repos.d/grafana.repo
`
[grafana]
name=grafana
baseurl=https://rpm.grafana.com
repo_gpgcheck=1
enabled=1
gpgcheck=1
gpgkey=https://rpm.grafana.com/gpg.key
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
`

# Update System ulang

```bash
sudo dnf update -y
```
## Install OSS grafana

```bash
sudo dnf install grafana
```
## Allow port 3000 in firewall
```bash
firewall-cmd --add-port=3000/tcp --permanent
sudo firewall-cmd --reload
```

## Start Service Daemon
```bash
sudo systemctl daemon-reload
sudo systemctl start grafana-server && sudo systemctl enable grafana-server
sudo systemctl status grafana-server
```
# Install Apache webserver

## Untuk install apache lakukan perintah berikut
```bash
sudo dnf install httpd -y
```

## Start dan enable Apache webserver
```bash
sudo systemctl enable httpd && sudo systemctl start httpd
sudo systemctl status httpd
```

## Buat Apache Virtual Host File dan Reverse Proxy
* Pembuatan Apache Virtual Host File dan reverse proxy ini agar grafana dapat diakses melalui domain. *
```bash
sudo nano /etc/httpd/conf.d/grafana.conf
```

## Configurasi Apache Virtual Host File
*//* Masukan kode berikut ke dalam Apache Virtual Host File

`<VirtualHost *:80>
     ServerName yourdomain.com
     DocumentRoot /var/www/html/
     <Directory /var/www/html/>
          Options FollowSymlinks
          AllowOverride All
          Require all granted
     </Directory>

    ProxyRequests off 
    ProxyPass / http://127.0.0.1:3000/ 
    ProxyPassReverse / http://127.0.0.1:3000/

     ErrorLog /var/log/httpd/yourdomain.com_error.log
     CustomLog /var/log/httpd/yourdomain.com.log combined
</VirtualHost>
`
## Cek Syntak
```bash
httpd -t
```

## Lalu restart service apache/httpd
```bash
sudo systemctl restart httpd
```
