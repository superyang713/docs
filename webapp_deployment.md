# Django webapp deployment on Linux server (DigitalOcean)
I have created a simple django webapp called learning_log. It has been tested
in the local development server. I went through a lot of pain to get it finally
deployed on the server hosted by DigitalOcean. So basically, this note is just
to remind me of what exactly I have done to get the webapp deployed and more
importantly, what each step means from my understanding.

## Goal
Deploy django application on DigitalOcean server in the following steps:
1. ssh to connect to the remote DigitalOcean server.
2. Create linux user and config terminal.
3. Install all necessary components.
4. Download django app repo.
5. Set up virtual working envrionment.
6. Create the production postgreSQL databsae.
7. Config django app settings.
8. Complete the django app initial setup and test it.
9. Test Gunicorn.
10. Config Gunicorn and test it again with the new service file.
11. Config Nginx.
12. Config domain namespace and direct it to the server ip.
13. Grab a beer. Your deserve it.

## Concept
Here is a oversimplied explanaton on Nginx, Gunicorn, socket, and wsgi.
Nginx faces the outside world. It serves static files (images, css, etc)
directly from the file system.
However, Nginx cannot talk directly to Django applications. Something is needed
to run the django application, feed the requests from web(Nginx) to django,
and return responses. That is where Gunicorn comes in. Gunicorn creates a Unix
socket, and serve responses to Nginx via the wsgi protocol - the socket passes
data in both directions:
```
The outside world <-> Nginx <-> The socket <-> Gunicorn <-> django <-> database
```
In other words, Nginx is the webserver, whereas Gunicorn is the appserver.

## Getting Started
Here is my django webapp file structure:
```
Learning_log
├── db.sqlite3
├── learning_log
│   ├── __init__.py
│   ├── learning_log
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── learning_logs
│   ├── admin.py
│   ├── apps.py
│   ├── forms.py
│   ├── __init__.py
│   ├── migrations
│   ├── models.py
│   ├── templates
│   ├── tests.py
│   ├── urls.py
│   └── views.py
├── manage.py
├── requirements.txt
└── users
    ├── admin.py
    ├── apps.py
    ├── __init__.py
    ├── migrations
    ├── models.py
    ├── templates
    ├── tests.py
    ├── urls.py
    └── views.py
```

A couple things to note here:
* The project name is learning_log. And there is also a folder with the same
  name inside the project. Make sure it keeps that way. The best practice is to
  make the repo name on Github have the same name as the project name, in order
  to avoid future confusion.
* Sqlite3 was used during development, whereas PostgreSQL is used for
  Deployment.

### Step 1: Set up remote server
* Create a Droplet named "django" (a virtual linux OS), add local SSH public key
to the droplet.
* Copy the Droplet IP address, and connect to the server. Login is not required
  because the local public ssh has been uploaded.
  ```
  $ ssh root@server-ip-address
  ```
* Create an alias to the droplet:
  ```
  $ vim /home/user/.ssh/config
  ```
  ```
  Host remote-django
      Hostname DROPLET_IP_ADDRESS
      User root
  ```

### Step 2: Create new linux user and personalize terminal
* Let us not use root, but instead, create a user called django, give the
  user sudo privileges, and switch to the new user account.
  ```
  $ adduser django
  $ usermod -aG sudo django
  $ su django
  ```
* Now it is time to config the terminal by installing Oh-my-zsh, personal
  vimrc and vim plugins.

### Step 3: Install all necessary components
* I am using python3, which you should, too, since django has dropped the
  support for python2. Install python3, postgreSQL and nginx via following
  commands:
  ```
  $ sudo apt update
  $ sudo apt-get install python3-pip python3-dev virtualenv libpq-dev postgresql
  postgresql-contrib nginx
  ```

### Step 4: Download django app repo.
  ```
  $ cd
  $ git clone https://github.com/superyang713/learning_log.git
  ```

### Step 5: Set up virtual working environment
* Now we need to work on the project in an isolated python envrionment.
  ```
  $ mkdir ~/VirtualEnvironment
  $ cd ~/VirtualEnvironment
  $ virtualenv --python=/usr/bin/python3 django
  $ source django/bin/activate
  ```
* Install all the required python packages for the django app development.
  ```
  pip install -r requirements.txt
  ```
* Finally, gunicorn also needs to be installed via pip (gunicorn is not
  included in my requirements.txt because I did not need it for app development
  in a local server)
  ```
  pip install gunicorn
  ```
* Note that gunicorn is installed via pip inside the virtual env, so the
  gunicorn executable, in this case, is at
  ```
  /home/django/VirtualEnvironment/django/bin
  ```
  This is really important.


### Step 6: Create the production postgreSQL databsae.
* Log into an interactive Postgre session.
  ```
  sudo -u postgres psql
  ```
* Inside the postgres shell, type:
  ```
  CREATE DATABASE learning_log;
  CREATE USER django WITH PASSWORD 'password';
  ALTER ROLE django SET client_encoding TO 'utf8';
  ALTER ROLE django SET default_transaction_isolation TO 'read committed';
  ALTER ROLE django SET timezone TO 'UTC';
  GRANT ALL PRIVILEGES ON DATABASE learning_log TO django;
  \q
  ```

### Step 7: Config django app settings.
* Open django project setting.
  ```
  vim ~/learning_log/learning_log/settings.py
  ```
* Add domain name and Droplet ip address to the ALLOWED_HOSTS.
  ```
  allowed_hosts = [
    '159.203.126.223',
    'yangdai.info',
    'www.yangdai.info'
  ]
  ```
* Modify the database params since the default is sqlite3.
  ```
  DATABASES = {
      'default': {
          'ENGINE': 'django.db.backends.postgresql_psycopg2',
          'NAME': 'learning_log',
          'USER': 'django',
          'PASSWORD': 'password',
          'HOST': 'localhost',
          'PORT': '',
      }
  }
  ```
* Near the end of the setting.py file, add a setting in the area of static files
  settings indicating where the static files should be placed. It is necessary
  so that Nginx can handle requests for these static items.
  ```
  STATIC_ROOT = os.path.join(BASE_DIR, 'static/')
  ```

### Step 8: Complete django app initial setup and test it.
* Migrate the initial database schema to our PostgreSQL database.
  ```
  cd ~/learning_log
  python manage.py makemigrations
  python manage.py migrate
  ```
* Create an admin user for the django webapp.
  ```
  python manage.py createsuperuser
  ```
* Collect all of the static content into a single directory we have configured
  in the setting.py file:
  ```
  python manage.py collectstatic
  ```
* (optional) Modify the gitignore file to exclude static folder from git repo.
* Now it is time to test if the project still works with the new database and
  settings, etc
  ```
  python manage.py runserver 0.0.0.0:8000
  ```
  In the local machine browser, visit the ip address followed by :8000
  ```
  http://server_ip:8000
  ```

### Step 9: Test Gunicorn
* Again, Gunicorn was installed in the virtual envrionment. Let's test it first
  to make sure it can serve the application before we leave the virtual
  envrionment.
  ```
  $ cd ~/learning_log
  $ gunicorn --bind 0.0.0.0:8000 learning_log.wsgi
  ```
  This will start Gunicorn on the same interface that the Django development
  server was running on. so we can go back to the browser and test the app
  again.
  As a reminder, Gunicorn does not know about static files. That is Nginx's
  job. Therefore, the webapp does not have css styling.
  wsgi is the entry point for Gunicorn to communicate with the django app.
* Hopefully the test is a success. Now it is time to get out of the virtual
  envrionment.
  ```
  $ deactivate
  ```

### Step 10: Config Gunicorn and test it again with the new service file.
* Gunicorn has been tested and it worked. So we need to config it to make it
  more robust for starting and stopping the application server. The best way to
  do this is to let Systemd manage Gunicorn, so we need to make a systemd
  service file for Gunicorn.
  All details has been explained in the comments in the service file.
  ```
  sudo vim /etc/systemd/system/gunicorn.service
  ```
  ```
  # Specify metadata and dependencies.
  # Tell init system to only start this after the networking target has been
  # reached.
  [Unit]
  Description=gunicorn daemon
  After=network.target

  # Give group ownership to www.data group so that Nginx can communicate easily
  # with Gunicorn.
  # Specify the full path to the Gunicorn executable. Note that gunicorn is
  # installed within virtual environment.
  # Instead of binding Gunicorn to a port like I did during test, I bind it to a
  # socket within the project directory because it is safer and faster. It has
  # been discussed in the concept section.
  [Service]
  User=django
  Group=www-data
  WorkingDirectory=/home/django/learning_log
  ExecStart=/home/django/VirtualEnvironment/django/bin/gunicorn \
  	--access-logfile - --workers 3 --bind \
  	unix:/home/django/learning_log/learning_log.sock \
  	learning_log.wsgi:application

  # Tell systemd what to link this service to if we enable it to start at boot.
  # We want this service to start when the regular multi-user system is running.
  [Install]
  WantedBy=multi-user.target
  ```
* Now that the service file has been set up, we need to start the Gunicorn
  service now and enable it to start at boot as well.
  ```
  sudo systemctl start gunicorn
  sudo systemctl enable gunicorn
  ```
* Check Gunicorn service status to see if everything is ok.
  ```
  sudo systemctl status gunicorn
  ```
  Next, check the existence of the learning_log.sock file within the project
  directory.
  ```
  $ ls /home/django/learning_log
  ```
  If gunicorn service is not working correctly, check the service log.
  ```
  sudo journalctl -u gunicorn
  ```
* Now that Gunicorn is up and running, it is listening to the socket and
  processing the traffic that is passed to the socket. So we need to config
  Nginx to pass traffic to the socket.

### Step 11: Config Nginx
* Start by creating and opening a new server block in Nginx's sites-available
  directory:
  ```
  $ sudo vim /etc/nginx/sites-available/learning_log
  ```
  ```
  server {
      listen 80;
      server_name 159.203.126.223 yangdai.info www.yangdai.info;

      # Ignore any problems with finding a favicon.
      location = /favicon.ico { access_log off; log_not_found off; }

      # Tell Nginx where to find the static assets.
      location /static/ {
          root /home/django/learning_log;
      }

      # Include default proxy_params and pass traffic to the socket.
      location / {
          include proxy_params;
          proxy_pass http://unix:/home/django/learning_log/learning_log.sock;
      }
  }
  ```
* Now enable the Ngnix service by linking the file to the sites-enabled
  directory:
  ```
  sudo ln -s /etc/nginx/sites-available/learning_log /etc/nginx/sites-enabled
  ```
* Test Nginx config for syntax errors:
  ```
  sudo nginx -t
  ```
* Restart Nginx:
  ```
  sudo systemctl restart nginx
  ```
* Open up firewall to normal traffic on port 80.
  ```
  sudo ufw allow 'Nginx Full'
  ```
  Go to server's ip address in the browser to view the application.

### Step 12: Config domain namespace and direct it to the server ip.
* Since I have a domain from Godaddy, I will skip the part how to get a domain.
* In DigitalOcean webpage, click "Create", select Domains/DNS.
* Create 3 NS record, and the values are:
  ```
  ns1.digitalocean.com
  ns2.digitalocean.com
  ns3.digitalocean.com
  ```
  The host name is the domain name.
* Create an A record. Direct yangdai.info to the server ip address.
* Create an A record. Direct www.yangdai.info it to the server ip address.

### Step 13:
Open the website in a browser, and grab a beer.

### To get an SSL certificate to secure the traffic, follow [this
tutorial](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-16-04)
