
---
title: "Kerberos for nginx"
---

## config

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

