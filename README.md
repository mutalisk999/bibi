## Bibi

**Bibi** is an e-commerce fullstack solution built with Flask. It includes e-commerce, social, and hot common modules. It stood the test of business operations, is a light but complete solution.

This project provides backend service, based on Flask, MongoDB, Redis, Celery, RabbitMQ, and supports Python 3.5.

Bibi offers fullstack solution for use with the following:

> [bibi-frontend](https://github.com/mutalisk999/bibi-frontend) Mobile web frontend

> [bibi-ionic](https://github.com/mutalisk999/bibi-ionic) Hybrid APP

* 可观看[中文文档](https://github.com/mutalisk999/bibi/blob/master/README_zh.md)

----------------

**Features**：

- [x] User
    - [x] Supports Email, Wechat, Weibo, QQ, and Facebook Oauth，[social-oauth](https://github.com/mutalisk999/social-oauth) for details
    - [x] User Information, Address, Contact, Favor collections and etc..
- [x] Social
    - [x] Post, like, comment, and bad information report.
    - [x] Following and followers.
    - [x] Notifications.
- [x] Content
    - [x] Products board
    - [x] Banners
- [x] Product
    - [x] Brands, categories, tags, vendors, price history.
    - [x] Commodities sub-selections based on different colors, sizes and materials.
- [x] Cart
    - [x] Session carts
    - [x] Snapshot for items
- [x] Order
    - [x] Snapshot for order, store items history
    - [x] Split into different packages depend on the total price and categories
    - [x] Logistics information tracking, automatic updates
    - [x] Third party logistics business parcel handling
- [x] Payment
    - [x] Supports Wechat，Paypal and etc..
    - [x] Supports coupons, coins for discount.
- [x] Management System

- [x] AWS file upload
- [x] Bing translate API
- [x] Openexchange rate API
- [x] Kuaidi100 logistics tracking API


#### Screenshot

Content Management
![](http://7xn6eu.com1.z0.glb.clouddn.com/Backend.png)
Logistics Management
![](http://7xn6eu.com1.z0.glb.clouddn.com/Logistics-Backend.jpg)
Product Management
![](http://7xn6eu.com1.z0.glb.clouddn.com/Item%20Backend.png)

#### Get Started

This based on Ubuntu/Debian，please skip if you had set up Python 3 environment.

```bash
# set up python3 environment
sudo apt-get update
sudo apt-get install python3-pip python3-dev
sudo apt-get install build-essential libssl-dev libffi-dev python-dev

sudo apt-get install mongodb-server redis-server redis-tools rabbitmq-server

# ubuntu 16.04
sudo apt-get install libssl-dev

# ubuntu 18.04 or later
sudo apt-get install libssl1.0-dev

# set up virtualenv
sudo pip3 install virtualenv virtualenvwrapper
echo "export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3" >> ~/.bashrc
echo "export WORKON_HOME=~/Env" >> ~/.bashrc
echo "source /usr/local/bin/virtualenvwrapper.sh" >> ~/.bashrc
source ~/.bashrc

# Now virtual env for python3 will be installed in ~/Env

mkvirtualenv bibi # rename bibi
workon bibi # activate bibi env
```

Install dependencies
```bash
pip3 install -r requirements.txt
```

Initial database
```python
python3 manage.py shell
# into Python3 shell
>>> from application.models import User
>>> user = User.create(email="xxxx@xxx.com", password="xxx", name="xxxx")
# Rename the email, password, name
>>> user.roles.append("ADMIN")
>>> user.save()
```

Run server
```
# start celery
celery -A application.cel worker -l info &

python3 manage.py runserver
```
Now open http://127.0.0.1:5000/admin/ on local.



#### Deploy
```bash
# set up supervisor
sudo apt-get install supervisor
# set up gunicorn
pip3 install gunicorn
```

Create service config

`sudo vi /etc/systemd/system/gunicorn.service`

```
[Unit]
Description=gunicorn service
After=network.target

[Service]
User=ubuntu
Group=www-data
WorkingDirectory=/home/ubuntu/bibi
ExecStart=/home/ubuntu/Env/bibi/bin/gunicorn --access-logfile - --workers 3 --bind 127.0.0.1:8080 application:create_app

[Install]
WantedBy=multi-user.target
```
PS: --workers  the workers number，formula：（CPUs*2 + 1)

To enable gunicorn service
```
sudo systemctl enable gunicorn.service
sudo systemctl start gunicorn.service
sudo systemctl status gunicorn.service
```

To reload gunicorn after config change
```
sudo systemctl daemon-reload
sudo systemctl restart gunicorn
```

Create nginx config

`sudo vim /etc/nginx/sites-enabled/bibi.conf`

```nginx
server {
    listen 80;
    server_name bigbang.maybi.cn;

    location / {
        proxy_pass http://127.0.0.1:8080; # Pointing to the gunicorn host
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }

  }
```

Start supervisor, nginx
```bash
sudo supervisorctl reload
sudo supervisorctl start bibi

sudo service nginx restart
```

Bravo! It's done.

Please open issues if you have problems.

-----------------------------------
#### License

Apache-2.0
