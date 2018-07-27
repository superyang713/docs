# Django webapp deployment on Linux server (DigitalOcean)
I have created a simple django webapp called learning_log. It has been tested
in the local development server. I went through a lot of pain to get it finally
deployed on the server hosted by DigitalOcean. So basically, this note is just to remind me what exactly I have done to get the webapp deployed and more
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
9. Install gunicorn in the virtual environment and set it up.
10. Config Nginx.
11. Config domain namespace and direct it to the server ip.
12. Grab a beer. Your deserve it.

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
* Create a Droplet named "django" a virtual linux OS), add local SSH public key
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

### Complete django app initial setup and test it.
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
* (optional) Modify the gitignore file to include static folder.
* Now it is time to test if the project still works with the new database and
  settings, etc
  ```
  python manage.py runserver 0.0.0.0:8000
  ```
  In the local machine browser, visit the ip address followed by :8000
  ```
  http://server_ip:8000
  ```









