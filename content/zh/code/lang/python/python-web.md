
---
title: "Python Web "
---

## std

```shell
python3 -m http.server 3333
python -m SimpleHTTPServer 3333
```

## fastapi
> Python 3.7+

http://127.0.0.1:8000/docs for https://github.com/swagger-api/swagger-ui
http://127.0.0.1:8000/redoc for https://github.com/Rebilly/ReDoc

```shell
pip install "fastapi[all]"
# use the 'app' object in module fastapi-main(or file called fastapi-main.py)
uvicorn fastapi-main:app --reload
```

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
async def root(secs: float):
    from time import sleep
    sleep(secs)
    return {"message": "Hello World"}
```

## flask

```shell
pip install Flask
python -m flask run 
```

```python
from flask import Flask, request

app = Flask(__name__)

@app.route("/")
def hello_world():
        secs = request.args.get("secs")
        from time import sleep
        sleep(float(secs))
        return {"message": "Hello World"}

if __name__ == '__main__':
    app.run(debug=True)
```

## django
```shell
pip install Django
django-admin startproject mysite
cd mysite
python manage.py runserver 0.0.0.0:8000
```

```python
```

## bottle
```python
from bottle import request, route, run, template
from time import sleep

@route('/hello/<name>')
def hello(name):
    return template('<b>Hello {{name}}</b>!', name=name)

@route('/')
def index():
    secs = request.query.get('secs')
    name = request.query.get('name')
    if secs:
        sleep(float(secs))
    if not name:
        name = "World
    return "Hello {}!".format(name)

run(host='0.0.0.0', port=8000)
```

## trollius

```python
import trollius as asyncio
from trollius import From

@asyncio.coroutine
def factorial(name, number):
    f = 1
    for i in range(2, number + 1):
        print("Task %s: Compute factorial(%d)..." % (name, i))
        yield From(asyncio.sleep(1))
        f *= i
    print("Task %s completed! factorial(%d) is %d" % (name, number, f))

loop = asyncio.get_event_loop()
tasks = [
    asyncio.async(factorial("A", 8)),
    asyncio.async(factorial("B", 3)),
    asyncio.async(factorial("C", 4))]
loop.run_until_complete(asyncio.wait(tasks))
loop.close()
```
