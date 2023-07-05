---
title: "Kerberos Install"
---

## server

```shell
apt install krb5-kdc krb5-admin-server krb5-config -y
# Default Kerberos version 5 realm: MY.LOCAL
# Kerberos servers for your realm: my.local
# Administrative server for your Kerberos realm: my.local
```

**config**

```shell
krb5_newrealm
# Enter KDC database master key: 78294181-8063-4BF7-B827-E56DB7E1622F

# enable remote login
sed -i '$a */admin *' /etc/krb5kdc/kadm5.acl
# systemctl restart krb5-kdc
/etc/init.d/krb5-kdc restart
/etc/init.d/krb5-admin-server restart

# kadmin.local add_principal -randkey myuser
kadmin.local add_principal -pw mypassword myuser
# kadmin.local delete_principal myuser
kadmin.local add_principal -pw mypassword myuser/admin
kadmin.local get_principal myuser
kadmin.local list_principals

klist
kinit myuser

# or kadmin -p root/admin -q 'ktadd ...'
kadmin.local ktadd -k /etc/myuser.keytab myuser@MY.LOCAL
```

**server port**
> `netstat -antup | grep krb`

- 88 krb5kdc: /usr/sbin/krb5kdc -P /var/run/krb5kdc.pid
- 464 kadmind: /usr/sbin/kadmind -P /var/run/kadmind.pid
- 749 kadmind

## client

```shell
apt install krb5-user
# Default Kerberos version 5 realm: MY.LOCAL
# Kerberos servers for your realm: my.local
# Administrative server for your Kerberos realm: my.local

# use server ip
echo "0.0.0.0 my.local" >> /etc/hosts

klist
kinit myuser
```

**add realm**
```shell
MY_REALM=MY.LOCAL
MY_DOMAIN=my.local

my_realm="\ $MY_REALM = {\n kdc = $MY_DOMAIN\n admin_server = $MY_DOMAIN\n }\n"
sed -i "/\[realms\]/a$my_realm" /etc/krb5.conf

my_domain_realm="\ .$MY_DOMAIN = $MY_REALM\n $MY_DOMAIN = $MY_REALM\n"
sed -i "/\[domain_realm\]/a$my_domain_realm" /etc/krb5.conf
```

## server for nginx

```shell
apt install nginx-extras libpam-krb5 -y

vi /etc/nginx/sites-available/default
cat <<EOF > /etc/pam.d/nginx-krb5
auth sufficient pam_krb5.so use_first_pass
account sufficient pam_krb5.so
EOF
/etc/init.d/nginx restart
```shell

```nginx
location /test {
    # request password authentication and use PAM module
    auth_pam "Kerberos Authentication";
    auth_pam_service_name "nginx-krb5";
}
```

```shell
cat <<EOF > /var/www/html/test/index.html
<html>
<body>
<div style="width: 100%; font-size: 40px; font-weight: bold; text-align: center;">
Test Page for Kerberos Auth
</div>
</body>
</html>
EOF

# use kerb account to access nginx
curl -v -u myuser:mypassword http://127.0.0.1/test/
```
