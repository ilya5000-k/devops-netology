1. Создайте виртуальную машину Linux.
```
vagrantfile:
```
```
Vagrant.configure("2") do |config|
    config.vm.box = "bento/ubuntu-20.04"
    config.vm.hostname = "vagrant"..
    config.vm.network "public_network", bridge: "eth1"
end
```
```
vagrant up
```
2. Установите ufw и разрешите к этой машине сессии на порты 22 и 443, при этом трафик на интерфейсе localhost (lo) должен ходить свободно на все порты.
```
sudo apt install ufw
sudo ufw status verbose
```
```
root@vagrant:/home/ilya# sudo ufw status verbose
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disabled (routed)
New profiles: skip

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW IN    Anywhere
443                        ALLOW IN    Anywhere
8200                       ALLOW IN    Anywhere
8201                       ALLOW IN    Anywhere
80                         ALLOW IN    Anywhere
22/tcp (v6)                ALLOW IN    Anywhere (v6)
443 (v6)                   ALLOW IN    Anywhere (v6)
8200 (v6)                  ALLOW IN    Anywhere (v6)
8201 (v6)                  ALLOW IN    Anywhere (v6)
80 (v6)                    ALLOW IN    Anywhere (v6)
```
3. Установите hashicorp vault .
```
apt-get install wget chrony curl apt-transport-https
```
```
Настроить время:
pool ntp.ubuntu.com        iburst maxsources 4
pool 0.ubuntu.pool.ntp.org iburst maxsources 1
pool 1.ubuntu.pool.ntp.org iburst maxsources 1
pool 2.ubuntu.pool.ntp.org iburst maxsources 2
```
```
systemctl enable chrony
```
```
Установить HashiCorp:
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
```
```
echo "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main" > /etc/apt/sources.list.d/hashicorp.list
```
```
apt-get update & apt-get install vault
```

4. Cоздайте центр сертификации по инструкции (ссылка) и выпустите сертификат для использования его в настройке веб-сервера nginx (срок жизни сертификата - месяц).
```
$ vault secrets enable \
    -path=pki_root_ca \
    -description="PKI Root CA" \
    -max-lease-ttl="262800h" \
    pki

$ vault write -format=json pki_root_ca/root/generate/internal \
    common_name="Root Certificate Authority" \
    country="Russian Federation" \
    locality="Moscow" \
    street_address="Red Square 1" \
    postal_code="101000" \
    organization="Horns and Hooves LLC" \
    ou="IT" \
    ttl="262800h" > pki-root-ca.json

$ cat pki-root-ca.json | jq -r .data.certificate > rootCA.pem

$ vault write pki_root_ca/config/urls \
    issuing_certificates="http://vagrant.test.com:8200/v1/pki_root_ca/ca" \
    crl_distribution_points="http://vagrant.test.com:8200/v1/pki_root_ca/crl"

$ vault secrets enable \
    -path=pki_int_ca \
    -description="PKI Intermediate CA" \
    -max-lease-ttl="175200h" \
    pki

$ vault write -format=json pki_int_ca/intermediate/generate/internal \
   common_name="Intermediate CA" \
   country="Russian Federation" \
   locality="Perm" \
   street_address="
Red Square 1
" \
   postal_code="614065" \
   organization="
Horns and Hooves LLC
" \
   ou="IT" \
   ttl="175200h" | jq -r '.data.csr' > pki_intermediate_ca.csr

$ vault write -format=json pki_root_ca/root/sign-intermediate csr=@pki_intermediate_ca.csr \
   country="Russia Federation" \
   locality="Perm" \
   street_address="
Red Square 1
" \
   postal_code="614065" \
   organization="
Horns and Hooves LLC
" \
   ou="IT" \
   format=pem_bundle \
   ttl="175200h" | jq -r '.data.certificate' > intermediateCA.cert.pem

$ vault write pki_int_ca/intermediate/set-signed \
    certificate=@intermediateCA.cert.pem

$ vault write pki_int_ca/config/urls \
    issuing_certificates="http://vagrant.test.com:8200/v1/pki_int_ca/ca" \
    crl_distribution_points="http://vagrant.test.com:8200/v1/pki_int_ca/crl"

$ vault write pki_int_ca/roles/example-dot-com-server \
    country="Russia Federation" \
    locality="Perm" \
    street_address="
Red Square 1
" \
    postal_code="614065" \
    organization="
Horns and Hooves LLC
" \
    ou="IT" \
    allowed_domains="test.com" \
    allow_subdomains=true \
    max_ttl="750h" \
    key_bits="2048" \
    key_type="rsa" \
    allow_any_name=false \
    allow_bare_domains=false \
    allow_glob_domain=false \
    allow_ip_sans=true \
    allow_localhost=false \
    client_flag=false \
    server_flag=true \
    enforce_hostnames=true \
    key_usage="DigitalSignature,KeyEncipherment" \
    ext_key_usage="ServerAuth" \
    require_cn=true

$ vault write pki_int_ca/roles/example-dot-com-client \
    country="Russia Federation" \
    locality="Perm" \
    street_address="
Red Square 1
" \
    postal_code="614065" \
    organization="
Horns and Hooves LLC
" \
    ou="IT" \
    allow_subdomains=true \
    max_ttl="750h" \
    key_bits="2048" \
    key_type="rsa" \
    allow_any_name=true \
    allow_bare_domains=false \
    allow_glob_domain=false \
    allow_ip_sans=false \
    allow_localhost=false \
    client_flag=true \
    server_flag=false \
    enforce_hostnames=false \
    key_usage="DigitalSignature" \
    ext_key_usage="ClientAuth" \
    require_cn=true

$ vault write -format=json pki_int_ca/issue/example-dot-com-server \
    common_name="vagrant.test.com" \
    alt_names="vagrant.test.com" \
    ttl="750h" > vagrant.test.com.crt

```

https://github.com/ilya5000-k/devops-netology/blob/main/kursovaya_4_1.png

https://github.com/ilya5000-k/devops-netology/blob/main/kursovaya_4_2.png

5. Установите корневой сертификат созданного центра сертификации в доверенные в хостовой системе.

https://github.com/ilya5000-k/devops-netology/blob/main/kursovaya_5_1.png

6. Установите nginx.

```
$ sudo apt install curl gnupg2 ca-certificates lsb-release ubuntu-archive-keyring

$ curl https://nginx.org/keys/nginx_signing.key | gpg --dearmor \
    | sudo tee /usr/share/keyrings/nginx-archive-keyring.gpg >/dev/null
    
$ echo "deb [signed-by=/usr/share/keyrings/nginx-archive-keyring.gpg] \
http://nginx.org/packages/ubuntu `lsb_release -cs` nginx" \
    | sudo tee /etc/apt/sources.list.d/nginx.list
    
$ sudo apt update && sudo apt install nginx

```
7. По инструкции (ссылка) настройте nginx на https, используя ранее подготовленный сертификат:
```
$cat /etc/nginx/conf.d/default.conf
server {
    listen       443 ssl;
    server_name  vagrant.test.com;
    ssl_certificate     vagrant.test.com.crt.pem;
    ssl_certificate_key vagrant.test.com.crt.key;
    ssl_protocols       TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers         HIGH:!aNULL:!MD5;

```
```
$ cp /etc/vault.d/vagrant.test.com.crt.pem /etc/nginx/vagrant.test.com.crt.pem
$ cp /etc/vault.d/vagrant.test.com.crt.key /etc/nginx/vagrant.test.com.crt.key
```
8. Откройте в браузере на хосте https адрес страницы, которую обслуживает сервер nginx.

https://github.com/ilya5000-k/devops-netology/blob/main/kursovaya_8_1.png

9. Создайте скрипт, который будет генерировать новый сертификат в vault:
```
#!/usr/bin/env bash

vault operator unseal xx6rAI2kLe0OdbZwYwpVxz9E71Q6Vd2gDbt7lSp/3j7q
vault operator unseal FUrw+Ao1Ey1yNVnLjuVCPfat1KdZ/KNxupMzmJZOvbOk
vault operator unseal rxlvDsIG8BgujGgVDAtKUlaUKYjbHOZFHTW0s/CRtw6O
vault login s.4n8dxoXsGb8FNHG8DVZx6lZO

vault write -format=json pki_int_ca/issue/example-dot-com-server \
    common_name="vagrant.test.com" \
    alt_names="vagrant.test.com" \
    ttl="745h" > /etc/vault.d/vagrant.test.com.crt

vault write pki_int_ca/tidy \
    safety_buffer=5s \
    tidy_cert_store=true \
    tidy_revocation_list=true

cat /etc/vault.d/vagrant.test.com.crt | jq -r .data.certificate > /etc/vault.d/vagrant.test.com.crt.pem
cat /etc/vault.d/vagrant.test.com.crt | jq -r .data.issuing_ca >> /etc/vault.d/vagrant.test.com.crt.pem
cat /etc/vault.d/vagrant.test.com.crt | jq -r .data.private_key > /etc/vault.d/vagrant.test.com.crt.key

rm /etc/nginx/vagrant.test.com.crt.pem
rm /etc/nginx/vagrant.test.com.crt.key

cp /etc/vault.d/vagrant.test.com.crt.pem /etc/nginx/vagrant.test.com.crt.pem
cp /etc/vault.d/vagrant.test.com.crt.key /etc/nginx/vagrant.test.com.crt.key

vault operator seal

nginx -s reload

```
10. Поместите скрипт в crontab, чтобы сертификат обновлялся какого-то числа каждого месяца в удобное для вас время.
```
$ crontab -e

# 25 числа каждого месяца
0 0 25 * * /home/ilya/vault/new_cert.sh

```
