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

