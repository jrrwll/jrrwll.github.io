
---
title: "mycli"
---

## install

```shell
pip install -U mycli
# mac
brew install mycli
# debian
sudo apt-get install mycli

mycli -d example_dsn
```

## config

```ini
# https://www.mycli.net/config
[main]
multi_line = True # sql must ends with ;
destructive_warning = True
log_file = ~/.mycli.log
log_level = INFO # DEBUG WARNING ERROR
# audit_log = ~/.mycli-audit.log
timing = True

table_format = ascii # vertical
syntax_style = default # colorful emacs monokai xcode
# \t - Product type
# \u - Username
# \h - Hostname of the server
# \d - Database name
# \n - Newline
prompt = '\u@\h:\d> '
less_chatty = True

[alias_dsn]
# example_dsn = mysql://[user[:password]@][host][:port][/dbname]
```