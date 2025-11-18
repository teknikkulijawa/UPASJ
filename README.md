# WIP
## Topology
```
[============]       [===========]
[=CONTROLLER=] ----> [==SERVER1==]
[============]       [===========]
```

## Step dibawah mulai dari controller dulu
1. Buat clone repository ini, install git dulu:
`apt install git -y`

2. Clone repository ini pake perintah:
`git clone https://github.com/teknikkulijawa/UPASJ`

3. Ganti nama folder UPASJ ini ke workfolder
`mv UPASJ workfolder`

4. Bisa cek catatan ini juga di foldernya dengan cara:
```
cd workfolder
nano README.md
```

5. Edit file `/etc/network/interfaces` di controller
`nano /etc/network/interfaces`

```
auto ens33
iface ens33 inet static
      address 192.168.69.2
```

6. Edit file `/etc/network/interfaces` di server
`nano /etc/network/interfaces`

```
auto ens33
iface ens33 inet static
      address 192.168.69.1
```

7. Install Ansible di controller

```
root@controller:~# apt install ansible sudo
root@controller:~# usermod -a -G sudo user
```

DNS config master `(/home/user/named.conf.local > Controller /etc/bind/named.conf.local)`

```
zone "cometstar.net.id" {
  type master;
  file "/etc/bind/db.internal";
  allow-transfer { 192.168.69.2; };
  also-notify { 192.168.69.2; };
};
```

Copy default DNS config (install bind9 on operator for config)

```
ops@operator:~$ sudo apt install bind9
ops@operator:~$ cp /etc/bind/db.local /home/ops/db.internal
```

DNS config record `(/etc/bind/db.internal)`

```
$TTL 604800
@   IN   SOA  cometstar.net.id. cometstar.net.id. (
                    2       ; Serial
               604800       ; Refresh
                86400       ; Retry
              2419200       ; Expire
               604800 )     ; Negative Cache TTL
;
@             IN  NS    controller.cometstar.net.id.
@             IN  A     192.168.69.2
www           IN  A     192.168.69.2
mail          IN  A     192.168.69.2
controller    IN  A     192.168.69.1
web           IN  A     192.168.69.2
operator      IN  A     192.168.69.3
```

Run playbook on operator with

```
ops@operator:~$ ansible-playbook -i hosts.ini -K create_300_users.yaml
ops@operator:~$ ansible-playbook -i hosts.ini -K dns_server.yaml
```

Check users on controller with

```
root@controller:~# ls /home
```

Install postfix, dovecot on web

```
root@web:~# apt install postfix dovecot-imapd dovecot-pop3d
root@web:~# dpkg-reconfigure postfix
```

Make sure Email has either STARTTLS or SSL/TLS authentication enabled and working.

Postfix config `/etc/postfix/main.cf`

```
home_mailbox = Maildir/
```

Postfix config `/etc/postfix/master.cf`

```
submission inet n - y - - smtpd
# -o syslog_name=postfix/submission
  -o smtpd_tls_security_level=encrypt
  -o smtpd_sasl_auth_enable=yes
```

```
smtps inet n - y - - smtpd
# -o syslog_name=postfix/smtps
  -o smtpd_tls_wrappermode=yes
  -o smtpd_sasl_auth_enable=yes
```

Dovecot config `/etc/dovecot/conf.d/10-mail.conf`

```
mail_location = maildir:~/Maildir
```

Dovecot config `/etc/dovecot/conf.d/10-ssl.conf`

```
ssl_cert = </etc/dovecot/private/dovecot.pem
ssl_key = </etc/dovecot/private/dovecot.key
```

Install web server

```
root@web:~# apt install apache2
```

Web server content

```
# echo “<h1>Welcome to the night sky</h1>” > /var/www/html/index.html
```

You can check index.html if it contains or if accessed shows “Welcome to the night sky” and check if https is working.

Install mariadb and roundcube

```
root@web:~# apt install mariadb-server
root@web:~# apt install roundcube
```

Copy apache vhost mail config from template

```
root@web:~# cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/mail.conf
root@web:~# a2ensite mail
```

`/etc/apache2/sites-available/mail.conf`

```
ServerName mail.cometstar.net.id

ServerAdmin webmaster@cometstar.net.id
DocumentRoot /var/lib/roundcube/public_html
```

Install mariadb and roundcube

```
root@web:~# a2ensite mail
```

`/etc/roundcube/config.inc.php`

```
$config['default_host'] = 'mail.cometstar.net.id';
$config['smtp_server'] = 'mail.cometstar.net.id';
$config['smtp_port'] = 25;
$config['smtp_user'] = '';
$config['smtp_pass'] = '';
```

ProFTPD is recommended, but if user has already configured vsftpd/pureftpd or similar
software skip to testing.

`/etc/proftpd/proftpd.conf`

```
DefaultRoot ~
```

Make sure FTP can login using users created by ansible and read/write into the nested
directory. You can use FileZilla program on Operator.

```
ops@operator:~$ sudo apt install filezilla
```
