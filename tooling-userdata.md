#!/bin/bash
mkdir /var/www/
sudo mount -t efs -o tls,accesspoint=fsap-044779d064f7280b1 fs-049e3d5e1f789eee1:/ /var/www/
yum install -y httpd 
systemctl start httpd
systemctl enable httpd
yum module reset php -y
yum module enable php:remi-7.4 -y
yum install -y php php-common php-mbstring php-opcache php-intl php-xml php-gd php-curl php-mysqlnd php-fpm php-json
systemctl start php-fpm
systemctl enable php-fpm
git clone https://github.com/citadelict/tooling2.git
mkdir /var/www/html
sudo cp -rf tooling2/html/*  /var/www/html/
cd tooling2
mysql -h citatech-db.c1wcugy04w3l.us-east-2.rds.amazonaws.com -u admin -p guessWhat232 toolingdb < tooling-db.sql
cd /home/ec2-user
sudo touch healthstatus
cd /var/www/html
sudo sed -i "s/$db = mysqli_connect('mysql.tooling.svc.cluster.local', 'admin', 'admin', 'tooling');/$db = mysqli_connect('citatech-db.c1wcugy04w3l.us-east-2.rds.amazonaws.com', 'admin', 'guessWhat232', 'toolingdb');/g" functions.php
chcon -t httpd_sys_rw_content_t /var/www/html/ -R
sudo systemctl restart httpd
sudo systemctl status httpd







