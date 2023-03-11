
---
title: "Nsenter"
---


## nsenter

```shell
wget https://www.kernel.org/pub/linux/utils/util-linux/v2.32/util-linux-2.32.tar.gz
tar -zxf util-linux-2.32.tar.gz &&  cd util-linux-2.32
./configure
make

# make && make install，可能影响操作系统底层工具
cp nsenter /usr/local/bin/

```

## `docker-enter`

```shell
#!/bin/sh

if [ -e $(dirname "$0")/nsenter ]; then
  # with boot2docker, nsenter is not in the PATH but it is in the same folder
  NSENTER=$(dirname "$0")/nsenter
else
  NSENTER=nsenter
fi

if [ -z "$1" ]; then
  echo "Usage: $(basename "$0") CONTAINER [COMMAND [ARG]...]"
  echo ""
  echo "Enters the Docker CONTAINER and executes the specified COMMAND."
  echo "If COMMAND is not specified, runs an interactive shell in CONTAINER."
else
  PID=$(docker inspect --format "{{.State.Pid}}" "$1")
  if [ -z "$PID" ]; then
    exit 1
  fi
  shift

  OPTS="--target $PID --mount --uts --ipc --net --pid --"

  if [ -z "$1" ]; then
    # No command given.
    # Use su to clear all host environment variables except for TERM,
    # initialize the environment variables HOME, SHELL, USER, LOGNAME, PATH,
    # and start a login shell.
    "$NSENTER" $OPTS su - root
  else
    # Use env to clear all host environment variables.
    "$NSENTER" $OPTS env --ignore-environment -- "$@"
  fi
fi
```
