
---
title: "Database client in Python"
---

## mysql

```python
# pip3 install PyMySQL
import pymysql

db = pymysql.connect("localhost","user","passwd","testdb" )
cursor = db.cursor()

cursor.execute("SELECT VERSION()")
print(f"Database version : {cursor.fetchone()}")

try:
   cursor.execute("DROP TABLE IF EXISTS EMPLOYEE")
   db.commit()
except:
   db.rollback()

cursor.execute("SELECT 1")
rows = cursor.fetchall()
for row in rows:
    print(row[0])

db.close()
```

## rabbitmq
```python
import pika

connection = pika.BlockingConnection(pika.ConnectionParameters('127.0.0.1', 5672))
channel = connection.channel()
channel.queue_declare(queue='hello')
channel.basic_publish(exchange='', routing_key='hello', body='Hello World!')
print("[x] Sent 'Hello World!'")
connection.close()
```
