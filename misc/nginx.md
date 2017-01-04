# Setting up and Working with NGINX

## Setup

Setting up should work out of the box:

```
[apt-get install | pacman -S | ... ] nginx

```

Make sure that you check everything is working.

```
$ systemctl status nginx | systemctl status nginx.service
$ netstat -tulpn | grep ':80'

```

Then to work with it, edit the sites-enabled in: ``/etc/nginx/sites-enabled/``

## Working with Python

### Installs

```
python3
python3-pip
python3-venv
```

Now you can pip install everything in a separate virtualenv:

```
$ python3 -m venv <directory>
$ pip3 install flask
$ pip3 install gunicorn
```

### Write your test application with python:

**Test.py**

```
from flask import Flask
application = Flask(__name__)

@application.route("/")
def hello():
    return "<h1>I'm a TEST! :D</h1>"

	if __name__ == "__main__":
	    application.run(host='0.0.0.0')
```


**wsgi.py**

```
from test import application

if __name__ == "__main__":
    application.run()
```

### Run Gunicorn

#### Test
```
gunicorn test:application -b 127.0.0.1:8001
```

#### Set up with NGINX

Run as Daemon in background

```
gunicorn test:application -b 127.0.0.1:8001 -D
```

Edit the config ``/etc/nginx/sites-enabled/default``, replacing ``location / {....}``:

```
location / {
               proxy_pass http://127.0.0.1:8001;
               proxy_set_header Host $host;
               proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      }
```

Then reload

```
systemctl reload nginx
```

Or restart

``` 
systemctrl restart nginx
```

---

## Working with NodeJS

### Installing and pre-requisites

Follow the guidelines, make sure to get ``npm`` with your nodejs.

Then install the program to run:

```
npm install pm2 -g
```

### Getting it working

Make a test javascript file

**hello.js**

```

var http = require('http');
var addr = '127.0.0.1';
var port = 8001
http.createServer(function(req, res) {
	res.writeHead(200, {'Content-Type': 'text/plain'});
	res.end('Hello, World!\n');
}).listen(port, addr);
console.log('Server started at ' + addr + ':' + port);

```

Then start node to test

```
node hello.js
```

Then start pm2

```
pm2 start hello.js
```

Once that is done, similar to above edit the NGINX config


Edit the config ``/etc/nginx/sites-enabled/default``, replacing ``location / {....}``:

```
location / {
               proxy_pass http://127.0.0.1:8001;
               proxy_set_header Host $host;
               proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      }
```

Then reload

```
systemctl reload nginx
```

Or restart

``` 
systemctrl restart nginx
```


