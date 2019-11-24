# todo_api_jango:

### A sample todo web app using DJango framework.

Note that if you are on OSX and you have Homebrew installed you can do

    brew install python3

After installing the correct version for your OS, you will need to make sure it is set up correctly. Open a terminal and type:

    python3

You should see something resembling the following:

    Python 3.7.1 (v3.5.1:37a07cee5969, Dec  5 2015, 21:12:44) 
    [GCC 4.2.1 (Apple Inc. build 5666) (dot 3)] on darwin
    Type "help", "copyright", "credits" or "license" for more information.
    >>> 

This is the interactive Python shell. Hit CTRL + D to exit it for now

## Setting Up the Environment

To avoid polluting our global scope with unecessary packages, we are going to use a virtual environment to store our packages. One excellent virtual environment manager available for free is virtualenv. We will be using Python's package manager pip to install this and other packages like Django which we will require later on. First, let's get virtualenv installed.

    pip install virtualenv

Once that is done, create a folder called projects anywhere you like then cd into it.

    mkdir projects
    cd projects

Once inside the projects folder, create another folder called hello. This folder will hold our app.

    mkdir todo_api_jango

At this point, we need to create the environment to hold our requirements. We will do this inside the todo_api_jango folder.

    virtualenv -p /usr/local/bin/python3 env


The -p switch tells virtualenv the path to the python version you want to use. Feel free to switch out the path after it with your own Python installation path. The name env is the environment name. You can also change it to something else which fits the name of your project.

Once that is done, you should have a folder called env inside your hello folder. Your structure should now look something like this.

    projects
    ├─hello
    │   ├── env

You are now ready to activate the environment and start coding!

    source env/bin/activate

You will see a prompt with the environment name. That means the environment is active.

    (env)

## Installing Django

This is a simple pip install. The latest Django version at the time of writing is Django 1.9.6

    pip install django

## Creating an App

Now that Django is installed, we can use its start script to create a skeleton project. This is as simple as using its admin script in the following way.

    django-admin startproject todo_api_jango

Note: Do not use django anywhere in the project name, it fails the build and creats confusion in the project commond.

Running this command creates a skeleton django app with the following structure:

    todo_api_jango
    ├─todo_api_jango
    │   ├── __init__.py
    │   ├── settings.py
    │   ├── urls.py
    │   └── wsgi.py
    └── manage.py


When you look into the todo_api_jango folder that was created, you will find a file called manage.py and another folder called todo_api_jango. This is your main project folder and contains the project's settings in a file called settings.py and the routes in your project in the file called urls.py. Feel free to open up the settings.py file to familiarize yourself with its contents.

Ready to move on? Excellent.

## Changing App Settings

Let's change a few settings. Open up the settings.py file in your favorite editor. Find a section called Installed Apps which looks something like this.

    # todo_api_jango/settings.py
    INSTALLED_APPS = [
        'django.contrib.admin',
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.messages',
        'django.contrib.staticfiles',
    ]

Django operates on the concept of apps. An app is a self contained unit of code which can be executed on its own. An app can do many things such as serve a webpage on the browser or handle user authentication or anything else you can think of. Django comes with some default apps preinstalled such as the authentication and session manager apps. Any apps we will create or third-party apps we will need will be added at the bottom of the Installed Apps list after the default apps installed.

Before we create a custom app, let's change the application timezone. Django uses the tz database timezones, a list of which can be found here.

The timezone setting looks like this.

    # todo_api_jango/settings.py
    TIME_ZONE = 'UTC'


## Creating your own app

It is important to note that Django apps follow the Model, View, Template paradigm. In a nutshell, the app gets data from a model, the view does something to the data and then renders a template containing the processed information. As such, Django templates correspond to views in traditional MVC and Django views can be likened to the controllers found in traditional MVC.

That being said, let's create an app. cd into the first todo_api_jango folder and type;

    python manage.py startapp home_app

Running this command will create an app called home_app. Your file structure should now look something like this.

    todo_api_jango
    ├── todo_api_jango
    │        ├── __init__.py
    │        ├── settings.py
    │        ├── urls.py
    │        └── wsgi.py
    ├── home
    │        ├── __init__.py
    │        ├── admin.py
    │        ├── apps.py
    │        ├── migrations
    │        ├── models.py
    │        ├── tests.py
    │        └── views.py
    └── manage.py


To get Django to recognize our brand new app, we need to add the app name to the Installed Apps list in our settings.py file.


# todo_api_jango/settings.py
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'home'
]


Once that is done, let's run our server and see what will be output. We mentioned that Django comes with a built in lightweight web server which, while useful during development, should never be used in production. Run the server as follows:

    python manage.py runserver

## Your output should resemble the following:

    Performing system checks...

    System check identified no issues (0 silenced).

    You have unapplied migrations; your app may not work properly until they are applied.
    Run 'python manage.py migrate' to apply them.

    November 24, 2019 - 16:36:01
    Django version 2.2.7, using settings 'todo_api_jango.settings'
    Starting development server at http://127.0.0.1:8000/
    Quit the server with CONTROL-C.

    [24/Nov/2019 16:36:09] "GET / HTTP/1.1" 200 16348
    [24/Nov/2019 16:36:09] "GET /static/admin/css/fonts.css HTTP/1.1" 200 423
    [24/Nov/2019 16:36:09] "GET /static/admin/fonts/Roboto-Bold-webfont.woff HTTP/1.1" 200 86184
    [24/Nov/2019 16:36:09] "GET /static/admin/fonts/Roboto-Light-webfont.woff HTTP/1.1" 200 85692
    [24/Nov/2019 16:36:09] "GET /static/admin/fonts/Roboto-Regular-webfont.woff HTTP/1.1" 200 85876



If you look carefully, you will see a warning that you have unapplied migrations. Ignore that for now. Go to your browser and access http://127.0.0.1:8000/. If all is running smoothly, you should see the Django welcome page.

We are going to replace this page with our own template. But first, let's talk migrations.

## Migrations


Migrations make it easy for you to change your database schema (model) without having to lose any data. Any time you create a new database model, running migrations will update your database tables to use the new schema without you having to lose any data or go through the tedious process of dropping and recreating the database yourself.

Django comes with some migrations already created for its default apps. If your server is still running, stop it by hitting CTRL + C. Apply the migrations by typing:

    python manage.py migrate

If successful, you will see an output resembling this one.

    Operations to perform:
    Apply all migrations: sessions, auth, contenttypes, admin
    Running migrations:
    Rendering model states... DONE
    Applying contenttypes.0001_initial... OK
    Applying auth.0001_initial... OK
    Applying admin.0001_initial... OK
    Applying admin.0002_logentry_remove_auto_add... OK
    Applying contenttypes.0002_remove_content_type_name... OK
    Applying auth.0002_alter_permission_name_max_length... OK
    Applying auth.0003_alter_user_email_max_length... OK
    Applying auth.0004_alter_user_username_opts... OK
    Applying auth.0005_alter_user_last_login_null... OK
    Applying auth.0006_require_contenttypes_0002... OK
    Applying auth.0007_alter_validators_add_error_messages... OK
    Applying sessions.0001_initial... OK


Running the server now will not show any warnings.

## Urls & Templates








