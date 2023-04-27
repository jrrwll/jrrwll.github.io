
---
title: "Auto-GPT"
---

## install

### container

```shell
# https://hub.docker.com/r/significantgravitas/auto-gpt
docker pull significantgravitas/auto-gpt:0.2.2
docker pull redis/redis-stack-server
```

**docker-compose.yml**

```yaml
version: "3.9"
services:
  auto-gpt:
    image: significantgravitas/auto-gpt
    depends_on:
      - redis
    env_file:
      - .env
    environment:
      MEMORY_BACKEND: ${MEMORY_BACKEND:-redis}
      REDIS_HOST: ${REDIS_HOST:-redis}
    volumes:
      - ./:/app
    profiles: ["exclude-from-up"]
  redis:
    image: "redis/redis-stack-server:latest"
```

