## Services

### nginx
```bash
sudo yum install yum-utils
```
```bash
# /etc/yum.repos.d/nginx.repo

[nginx-stable]
name=nginx stable repo
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=1
enabled=1
gpgkey=https://nginx.org/keys/nginx_signing.key
module_hotfixes=true
```
```bash
sudo yum install nginx
```
### syslog-ng
```bash
cd /etc/yum.repos.d/
wget https://copr.fedorainfracloud.org/coprs/czanik/syslog-ng48/repo/epel-9/czanik-syslog-ng48-epel-9.repo
dnf install syslog-ng
systemctl enable syslog-ng && systemctl start syslog-ng
```
### zabbix
```bash
# Add this line in the end of [epel] section /etc/yum.repos.d/epel.repo
# excludepkgs=zabbix*

rpm -Uvh https://repo.zabbix.com/zabbix/7.0/rocky/9/x86_64/zabbix-release-7.0-5.el9.noarch.rpm
dnf clean all

dnf module enable php:8.2
dnf install php php-cli php-common php-fpm
dnf module enable nginx:1.24
dnf install nginx

dnf install zabbix-server-pgsql zabbix-web-pgsql zabbix-sql-scripts
dnf install zabbix-selinux-policy zabbix-nginx-conf
dnf install zabbix-agent2

# Initialize database
sudo -u postgres createuser --pwprompt zabbix
sudo -u postgres createdb -O zabbix zabbix
zcat /usr/share/zabbix-sql-scripts/postgresql/server.sql.gz | sudo -u zabbix psql zabbix

# Add to /etc/zabbix/zabbix_server.conf
# DBPassword=pa$$w0rd

# Add to /etc/nginx/conf.d/zabbix.conf
# listen 8080;
# server_name zabbix.example.com;

# Enable and start services
systemctl restart zabbix-server zabbix-agent nginx php-fpm
systemctl enable zabbix-server zabbix-agent nginx php-fpm
```
### freeradius
```bash
rpm --import 'https://packages.networkradius.com/pgp/packages%40networkradius.com'

cat <<'EOF' >/etc/yum.repos.d/networkradius.repo
[networkradius]
name=NetworkRADIUS-$releasever
baseurl=http://packages.networkradius.com/freeradius-3.2/rocky/$releasever/
enabled=1
gpgcheck=1
gpgkey=https://packages.networkradius.com/pgp/packages%40networkradius.com
EOF

yum install yum-utils
yum config-manager --enable crb
dnf install freeradius
dnf install freeradius-config freeradius-sqlite freeradius-utils

# Directory: /etc/raddb
```
