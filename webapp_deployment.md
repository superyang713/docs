# Django webapp deployment on Linux server (DigitalOcean)
I have created a simple django webapp called learning_log. It has been tested
in the local development server. I went through a lot of pain to get it finally
deployed on the server hosted by DigitalOcean. So basically, this note is just
to remind me what exactly I have done to get the webapp deployed and more
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
8. Install gunicorn in the virtual environment and set it up.
9. Config Nginx.
10. Config domain namespace and direct it to the server ip.
11. Grab a beer. Your deserve it.

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
  executable, in this case, is at
  ```
  /home/django/VirtualEnvironment/django/bin
  ```
  This is really important.








### Prerequisites

What things you need to install the software and how to install them

```
Give examples
```

### Installing

A step by step series of examples that tell you how to get a development env running

Say what the step will be

```
Give the example
```

And repeat

```
until finished
```

End with an example of getting some data out of the system or using it for a little demo

## Running the tests

Explain how to run the automated tests for this system

### Break down into end to end tests

Explain what these tests test and why

```
Give an example
```

### And coding style tests

Explain what these tests test and why

```
Give an example
```

## Deployment

Add additional notes about how to deploy this on a live system

## Built With

* [Dropwizard](http://www.dropwizard.io/1.0.2/docs/) - The web framework used
* [Maven](https://maven.apache.org/) - Dependency Management
* [ROME](https://rometools.github.io/rome/) - Used to generate RSS Feeds

## Contributing

Please read [CONTRIBUTING.md](https://gist.github.com/PurpleBooth/b24679402957c63ec426) for details on our code of conduct, and the process for submitting pull requests to us.

## Versioning

We use [SemVer](http://semver.org/) for versioning. For the versions available, see the [tags on this repository](https://github.com/your/project/tags).

## Authors

* **Billie Thompson** - *Initial work* - [PurpleBooth](https://github.com/PurpleBooth)

See also the list of [contributors](https://github.com/your/project/contributors) who participated in this project.

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details

## Acknowledgments

* Hat tip to anyone whose code was used
* Inspiration
* etc
https://gist.github.com/Atem18/4696071
