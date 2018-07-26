# Django webapp deployment on Linux server (DigitalOcean)
I have created a simple django webapp called learning_log. It has been tested
in the local development server. I went through a lot of pain to get it finally
deployed on the server hosted by DigitalOcean. So basically, this note is just
to remind me what exactly I have done to get the webapp deployed and more
importantly, what each step means from my understanding.

## Goal
Deploy django application on DigitalOcean server in the following steps:
1. ssh to connect to the remote DigitalOcean server.
2. Install all necessary components.
3. Create linux user.
4. Download django app repo.
5. Set up working envrionment.
6. Config django app settings.
7. Install gunicorn in the virtual environment and set it up.
8. Config Nginx.
9. Config domain namespace and direct it to the server ip.
10. Grab a beer. Your deserved it.



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
