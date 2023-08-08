# Initial Setup

In this project, I will use PyCharm to create the project `Build a Book Store`.  
It will auto generate the folder environment `venv`.  

Open the new termial, we will install `flask` by this command:
```commandline
pip install flask
```
The result likes this:
```commandline
$ pip install flask
Collecting flask
  Using cached Flask-2.3.2-py3-none-any.whl (96 kB)
Collecting Werkzeug>=2.3.3
  Using cached Werkzeug-2.3.6-py3-none-any.whl (242 kB)
Collecting Jinja2>=3.1.2
  Using cached Jinja2-3.1.2-py3-none-any.whl (133 kB)
Collecting itsdangerous>=2.1.2
  Using cached itsdangerous-2.1.2-py3-none-any.whl (15 kB)
Collecting click>=8.1.3
  Downloading click-8.1.6-py3-none-any.whl (97 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 97.9/97.9 kB 1.3 MB/s eta 0:00:00
Collecting blinker>=1.6.2
  Using cached blinker-1.6.2-py3-none-any.whl (13 kB)
Collecting MarkupSafe>=2.0
  Using cached MarkupSafe-2.1.3-cp311-cp311-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (28 kB)
Installing collected packages: MarkupSafe, itsdangerous, click, blinker, Werkzeug, Jinja2, flask
Successfully installed Jinja2-3.1.2 MarkupSafe-2.1.3 Werkzeug-2.3.6 blinker-1.6.2 click-8.1.6 flask-2.3.2 itsdangerous-2.1.2

[notice] A new release of pip available: 22.3.1 -> 23.2.1
[notice] To update, run: pip install --upgrade pip
```

Create the new file `app.py`. Now, we're going to create a REST API endpoint.

File `app.py`

```commandline
from flask import Flask

app = Flask(__name__)

stores = [
    {
        "name": "My Store",
        "items": [
            {
                "name": "Chair",
                "price": 15.99
            }
        ]
    }
]

@app.get("/store")
def get_store():
    return {"stores": stores}
```

Run that file by this command: `flask run`
```commandline
$ flask run
 * Debug mode: off
WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
 * Running on http://127.0.0.1:5000
Press CTRL+C to quit
```  
Access the following address to get data `http://127.0.0.1:5000/store`.  

The result should be like this:
```commandline
{"stores":[{"items":[{"name":"Chair","price":15.99}],"name":"My Store"}]}
```

![img.png](imgs%2Fimg.png)

## Interact with and Test Your REST API

I will use the `Postman Desktop` to test my API.  

![img_1.png](imgs%2Fimg_1.png)

#### Create new Store  
Next, I will create the new store by methode `POST`
File `app.py`
```commandline
from flask import Flask, request

app = Flask(__name__)

stores = [
    {
        "name": "My Store",
        "items": [
            {
                "name": "Chair",
                "price": 15.99
            }
        ]
    }
]

@app.get("/store")
def get_store():
    return {"stores": stores}


@app.post("/store")
def create_store():
    request_data = request.get_json()
    new_store = {"name": request_data["name"], "item": []}
    stores.append(new_store)
    return new_store, 201
```

Run again, and in the `Postman desktop`, crate new method `POST` to create new store.  

![img_2.png](imgs%2Fimg_2.png)

When send that method, we check again my store by method `GET`  

![img_3.png](imgs%2Fimg_3.png)

#### Create new Item  
Now, we will create new item in Store by adding the new method `POST`

File `app.py`
```
from flask import Flask, request

app = Flask(__name__)

stores = [
    {
        "name": "My Store",
        "items": [
            {
                "name": "Chair",
                "price": 15.99
            }
        ]
    }
]

@app.get("/store")
def get_store():
    return {"stores": stores}


@app.post("/store")
def create_store():
    request_data = request.get_json()
    new_store = {"name": request_data["name"], "items": []}
    stores.append(new_store)
    return new_store, 201


@app.post("/store/<string:name>/item")
def create_item(name):
    request_data = request.get_json()
    for store in stores:
        if store["name"] == name:
            new_item = {"name": request_data["name"], "price": request_data["price"]}
            store["items"].append(new_item)
            return new_item, 201
    return {"message": "Store not found"}, 404
```
In `Postman Desktop`, create new method `POST`  

![img_4.png](imgs%2Fimg_4.png)  

#### Get a specific store and the store items  

File `app.py`  

```commandline
from flask import Flask, request

app = Flask(__name__)

stores = [
    {
        "name": "My Store",
        "items": [
            {
                "name": "Chair",
                "price": 15.99
            }
        ]
    }
]

@app.get("/store")
def get_store():
    return {"stores": stores}


@app.post("/store")
def create_store():
    request_data = request.get_json()
    new_store = {"name": request_data["name"], "items": []}
    stores.append(new_store)
    return new_store, 201


@app.post("/store/<string:name>/item")
def create_item(name):
    request_data = request.get_json()
    for store in stores:
        if store["name"] == name:
            new_item = {"name": request_data["name"], "price": request_data["price"]}
            store["items"].append(new_item)
            return new_item, 201
    return {"message": "Store not found"}, 404


@app.get("/store/<string:name>")
def get_store_(name):
    for store in stores:
        if store["name"] == name:
            return store
    return {"message": "Store not found"}, 404


@app.get("/store/<string:name>/item")
def get_item_in_store(name):
    for store in stores:
        if store["name"] == name:
            return {"items": store["items"], "message": "You rock!"}
    return {"message": "Store not found"}, 404
```

In the `Postman` add 2 more methods like below:

![img_5.png](imgs%2Fimg_5.png)

![img_6.png](imgs%2Fimg_6.png)  

### Run a Flask App in a Docker Container

First of all, install `Python` with `flask` already installed is with a `Dockerfile`  

File `Dockerfile`

```commandline
FROM python:3.11
EXPOSE 5000
WORKDIR /app
RUN pip install flask
COPY . .
ENTRYPOINT [ "flask", "run", "--host", "0.0.0.0" ]
```

Then, in the same directory as the `Dockerfile`, run the following two commands:  

```commandline
$ sudo docker build -t rest-api-flask-python .
$ sudo docker run -it rest-api-flask-python
```

The result should like this:

```commandline
$ sudo docker build -t rest-api-flask-python .
[+] Building 22.5s (9/9) FINISHED                                                                                                                                                      docker:default
 => [internal] load build definition from Dockerfile                                                                                                                                             0.0s
 => => transferring dockerfile: 160B                                                                                                                                                             0.0s
 => [internal] load .dockerignore                                                                                                                                                                0.0s
 => => transferring context: 2B                                                                                                                                                                  0.0s
 => [internal] load metadata for docker.io/library/python:3.11                                                                                                                                   3.1s
 => [1/4] FROM docker.io/library/python:3.11@sha256:9a1b705aecedc76e8bf87dfca091d7093df3f2dd4765af6c250134ce4298a584                                                                            15.5s
 => => resolve docker.io/library/python:3.11@sha256:9a1b705aecedc76e8bf87dfca091d7093df3f2dd4765af6c250134ce4298a584                                                                             0.0s
 => => sha256:608c79ebc6d50c0de774945744542f1ac114478446b0b35ad4a15c3198743113 7.53kB / 7.53kB                                                                                                   0.0s
 => => sha256:9a1b705aecedc76e8bf87dfca091d7093df3f2dd4765af6c250134ce4298a584 2.14kB / 2.14kB                                                                                                   0.0s
 => => sha256:0ad2659187130a5b7a8094996b4f240078b97be75a5395fa9869ca0a178fa676 2.01kB / 2.01kB                                                                                                   0.0s
 => => sha256:bd36c7bfe5f4bdffcc0bbb74b0fb38feb35c286ea58b5992617fb38b0c933603 64.11MB / 64.11MB                                                                                                 6.2s
 => => sha256:785ef8b9b236a5f027f33cae77513051704c0538bff455ff5548105c954c3b1c 49.56MB / 49.56MB                                                                                                 3.8s
 => => sha256:5a6dad8f55ae6c733e986316bd08205c8b2c41640bf8d08ff6e9bbcb6884304f 24.03MB / 24.03MB                                                                                                 2.5s
 => => sha256:4d207285f6d296b9806bd00340437406c25207412c52fcfcbf229a5ecff7bf94 211.03MB / 211.03MB                                                                                              11.4s
 => => extracting sha256:785ef8b9b236a5f027f33cae77513051704c0538bff455ff5548105c954c3b1c                                                                                                        0.9s
 => => sha256:9402da1694b8dae94a0cb89a2719ce24a909e809b22c31d39edee8e18b3d300b 6.39MB / 6.39MB                                                                                                   4.6s
 => => sha256:9bdbf45d01af5880bbab0120b825cc0f237835fd253f54a79b396519ea250daf 19.76MB / 19.76MB                                                                                                 6.5s
 => => extracting sha256:5a6dad8f55ae6c733e986316bd08205c8b2c41640bf8d08ff6e9bbcb6884304f                                                                                                        0.3s
 => => extracting sha256:bd36c7bfe5f4bdffcc0bbb74b0fb38feb35c286ea58b5992617fb38b0c933603                                                                                                        1.2s
 => => sha256:dd8b7ef87a9d8f73f1da40f467d7878182e591a6ab390005d401c4e59928c8e2 244B / 244B                                                                                                       6.5s
 => => sha256:4de52e7027c53cdd2ad280aed34a7fb1b8ccb8143bd1ed4678f25a4d7020b5fa 3.09MB / 3.09MB                                                                                                   7.1s
 => => extracting sha256:4d207285f6d296b9806bd00340437406c25207412c52fcfcbf229a5ecff7bf94                                                                                                        3.1s
 => => extracting sha256:9402da1694b8dae94a0cb89a2719ce24a909e809b22c31d39edee8e18b3d300b                                                                                                        0.1s
 => => extracting sha256:9bdbf45d01af5880bbab0120b825cc0f237835fd253f54a79b396519ea250daf                                                                                                        0.3s
 => => extracting sha256:dd8b7ef87a9d8f73f1da40f467d7878182e591a6ab390005d401c4e59928c8e2                                                                                                        0.0s
 => => extracting sha256:4de52e7027c53cdd2ad280aed34a7fb1b8ccb8143bd1ed4678f25a4d7020b5fa                                                                                                        0.1s
 => [internal] load build context                                                                                                                                                                0.2s
 => => transferring context: 23.18MB                                                                                                                                                             0.1s
 => [2/4] WORKDIR /app                                                                                                                                                                           0.3s
 => [3/4] RUN pip install flask                                                                                                                                                                  3.1s
 => [4/4] COPY . .                                                                                                                                                                               0.2s
 => exporting to image                                                                                                                                                                           0.2s
 => => exporting layers                                                                                                                                                                          0.2s
 => => writing image sha256:a8747debb9db865c5b831f8b6cfddd273ebb17291f5f39f77704c2ffc27a016a                                                                                                     0.0s
 => => naming to docker.io/library/rest-api-flask-python                                                  
``` 

Check docker images by this command: `sudo docker images`

```commandline
$ sudo docker images
REPOSITORY                                      TAG       IMAGE ID       CREATED              SIZE
rest-api-flask-python                           latest    a8747debb9db   About a minute ago   1.05GB
```

```commandline
$ sudo docker run -it rest-api-flask-python
 * Debug mode: off
WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
 * Running on all addresses (0.0.0.0)
 * Running on http://127.0.0.1:5000
 * Running on http://172.17.0.2:5000
Press CTRL+C to quit
```

As we can see this address "http://172.17.0.2:5000". It is the address of container docker.