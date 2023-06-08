
---
title: "Pulsar"
---

## install


```shell
docker run -it -d --name pulsar \
    -p 6650:6650 -p 8080:8080 \
    --mount source=pulsardata,target=/pulsar/data \
    --mount source=pulsarconf,target=/pulsar/conf \
    apachepulsar/pulsar:3.0.0 bin/pulsar standalone

# uid 10000 for pulsar
sudo chown -R 10000:$(id -g -n) pulsardata

curl http://localhost:8080/admin/v2/persistent/public/default/my-topic/stats | python -m json.tool
```

## client

### python

```python
# pip install pulsar-client
import pulsar

client = pulsar.Client('pulsar://localhost:6650')

producer = client.create_producer('my-topic')
for i in range(10):
producer.send(('hello-pulsar-%d' % i).encode('utf-8'))

consumer = client.subscribe('my-topic', subscription_name='my-sub')
while True:
msg = consumer.receive()
print("Received message: '%s'" % msg.data())
consumer.acknowledge(msg)

client.close()
```
