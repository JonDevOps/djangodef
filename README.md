> A batteries-included Django starter project. To learn more visit [LearnDjango.com](https://learndjango.com).


https://github.com/wsvincent/djangox/assets/766418/a73ea730-a7b4-4e53-bf51-aa68f6816d6a


## 🚀 Features

- Django 5.0 & Python 3.12
- Install via [Pip](https://pypi.org/project/pip/) or [Docker](https://www.docker.com/)
- User log in/out, sign up, password reset via [django-allauth](https://github.com/pennersr/django-allauth)
- Static files configured with [Whitenoise](http://whitenoise.evans.io/en/stable/index.html)
- Styling with [Bootstrap v5](https://getbootstrap.com/)
- Debugging with [django-debug-toolbar](https://github.com/jazzband/django-debug-toolbar)
- DRY forms with [django-crispy-forms](https://github.com/django-crispy-forms/django-crispy-forms)
- Custom 404, 500, and 403 error pages
----

## Table of Contents
* **[Installation](#installation)**
  * [Pip](#pip)
  * [Docker](#docker)
* [Next Steps](#next-steps)
* [Contributing](#contributing)
* [Support](#support)
* [License](#license)

----

## 📖 Installation
DjangoX can be installed via Pip or Docker. To start, clone the repo to your local computer and change into the proper directory.

```
$ git clone https://github.com/wsvincent/djangox.git
$ cd djangox
```

### Pip

```
$ python -m venv .venv

# Windows
$ Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
$ .venv\Scripts\Activate.ps1

# macOS
$ source .venv/bin/activate

(.venv) $ pip install -r requirements.txt
(.venv) $ python manage.py migrate
(.venv) $ python manage.py createsuperuser
(.venv) $ python manage.py runserver
# Load the site at http://127.0.0.1:8000
```

### Docker

To use Docker with PostgreSQL as the database update the `DATABASES` section of `django_project/settings.py` to reflect the following:

```python
# django_project/settings.py
DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.postgresql",
        "NAME": "postgres",
        "USER": "postgres",
        "PASSWORD": "postgres",
        "HOST": "db",  # set in docker-compose.yml
        "PORT": 5432,  # default postgres port
    }
}
```

The `INTERNAL_IPS` configuration in `django_project/settings.py` must be also be updated:

```python
# config/settings.py
# django-debug-toolbar
import socket
hostname, _, ips = socket.gethostbyname_ex(socket.gethostname())
INTERNAL_IPS = [ip[:-1] + "1" for ip in ips]
```

And then proceed to build the Docker image, run the container, and execute the standard commands within Docker.

```
$ docker-compose up -d --build
$ docker-compose exec web python manage.py migrate
$ docker-compose exec web python manage.py createsuperuser
# Load the site at http://127.0.0.1:8000
```

## Next Steps

- Add environment variables. There are multiple packages but I personally prefer [environs](https://pypi.org/project/environs/).
- Add [gunicorn](https://pypi.org/project/gunicorn/) as the production web server.
- Update the [EMAIL_BACKEND](https://docs.djangoproject.com/en/4.0/topics/email/#module-django.core.mail) and connect with a mail provider.
- Make the [admin more secure](https://opensource.com/article/18/1/10-tips-making-django-admin-more-secure).
- `django-allauth` supports [social authentication](https://django-allauth.readthedocs.io/en/latest/providers.html) if you need that.

I cover all of these steps in tutorials and premium courses over at [LearnDjango.com](https://learndjango.com).

----

## 🤝 Contributing

Contributions, issues and feature requests are welcome! See [CONTRIBUTING.md](https://github.com/wsvincent/djangox/blob/master/CONTRIBUTING.md).

## ⭐️ Support

Give a ⭐️  if this project helped you!

## License

[The MIT License](LICENSE)



Jondevops notes

How to deploy the DjangoX template on Fly.io with a Postgres Database
SOURCES: 
https://fly.io/django-beats/deploying-django-to-production/


Step A
Create a repository on github and clone it locally

Step B
Download github.com/wsvincent/djangox and extract its contents into the repo we just cloned(itll be the project used for the tutorial. Make sure to actually extract or clone it but dont copy the files over because you'll miss the hidden files thatstart with ".").

Step C
Create a virtual environment within the root directory and install requirements.txt.
```$ python -m venv .venv``` 
and activate it 
```$ source .venv/bin/activate```
```(.venv) $ pip install -r requirements.txt```

Step D 
Create a local database
1st 
Install PostgreSQL and psycopg2
***WARNING: Use `psql --version` and `dpkg -l | grep postgresql-contrib` to check to make sure you dont already have postgresql or postgresql-contrib installed on your system***
```Bash
$sudo apt update
$sudo apt install postgresql postgresql-contrib
```

***WARNING: Use `pip list` to check to make sure you dont already have psycopg2, psycopg, psycopg2-binaries, or psycopg-binaries installed before installing another psycopg package or else there could be conflicts***
And install psycopg in your virtual environment
```Bash
$pip install psycopg

```

2nd 
Switch to the PostgreSQL user (the default is `postgres`):
```bash
$ sudo -i -u postgres
```

3rd
Create a new PostgreSQL database 
3.1
Access the PostgreSQL prompt:
```bash
$ psql
```

3.2
Create a new database: Replace mydatabase with your desired database name:
```sql
CREATE DATABASE mydatabase;
```

3.3
Create a new user: Replace myuser with your desired username and mypassword with your desired password:
```sql
CREATE USER myuser WITH PASSWORD 'mypassword';
```

3.4
Grant privileges to the user: Allow the user to access the database:
```sql
GRANT ALL PRIVILEGES ON DATABASE mydatabase TO myuser;
```

3.5. 
Exit the PostgreSQL prompt:
```
\q
```

4th
Update Django Settings
In your Django project, update the DATABASES setting in settings.py to use the new PostgreSQL database:
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'mydatabase',
        'USER': 'myuser',
        'PASSWORD': 'mypassword',
        'HOST': '127.0.0.1',  # Set to your host.Leaving as 'localhost' may break applying migrations 
        'PORT': '5432',       # Default PostgreSQL port
    }
}
```

5th
Apply migrations

```bash
python manage.py makemigrations
```
``` bash
python manage.py migrate
```

6th
Take note your connection string it looks like this postgres://USER:PASSWORD@HOST:PORT/NAME
```plaintext
postgres://myuser:mypassword@localhost:5432/mydatabase
```

Step E Generate a secret key
Open a Python shell:
```bash
python
```

Run the following commands:
```python
python
from django.core.management.utils import get_random_secret_key
secret_key = get_random_secret_key()
print(secret_key)
```

This will generate and print a new secret key. You can then copy this key and use it in your Django settings.

step F
Envionment Variables
1st Install environs because we want to store the configuration separate from our code and load them at runtime. This allow us to keep one settings.py file and still have multiple environments (i.e. local/staging/production)
```$ python -m pip install django-environ==0.9.0```

2nd
In our settings.py file, near the top, we can define the casting and default values for specific variables, for example, setting DEBUG to False by default.
```
# settings.py
from pathlib import Path
import environ  # <-- Updated!

env = environ.Env(  # <-- Updated!
    # set casting, default value
    DEBUG=(bool, False),
)
```

3rd
Create a .env file and add it to .gitignore. django-environ can take environment variables from the .env file.

4th
Update the settings.py to make sure it is taking the environment variables from the .env file:
```
# settings.py
# Build paths inside the project like this: BASE_DIR / 'subdir'.
BASE_DIR = Path(__file__).resolve().parent.parent

# Take environment variables from .env file
environ.Env.read_env(BASE_DIR / '.env')  # <-- Updated!
```

5th
Set specific environment variables in the .env file and input secret key you generated:
```
# .env
SECRET_KEY= #<--input secret key you generated here
DEBUG=True
```

6th
Update settings.py to be able to read SECRET_KEY and DEBUG from our environment variables:

```
# settings.py
# SECURITY WARNING: keep the secret key used in production secret!
SECRET_KEY = env('SECRET_KEY')  # <-- Updated!

# SECURITY WARNING: don't run with debug turned on in production!
DEBUG = env('DEBUG')  # <-- Updated!
```

7th
The last environment variable to be set is the DATABASE_URL.env.db() and will read from the DATABASE_URL variable
```
# settings.py
DATABASES = {
    # read os.environ['DATABASE_URL']
    'default': env.db()  # <-- Updated!
}
```

8th
Define your local database, adding it to the .env file:
```
# .env
SECRET_KEY= #<--input secret key you generated here
DEBUG=True
DATABASE_URL=postgres://USER:PASSWORD@HOST:PORT/NAME  # <-- Updated!
```

9th
Install Psycopg, to interact with our Postgres database. Make sure to check and make sure its not installed already before installing it. Also use the source instead of the binary for production:
```
python -m pip install psycopg2==2.9.5

```

10th
Install Gunicorn
When starting a Django project (with startproject management command), we get a minimal WSGI configuration set up out of the box. However, this default webserver is not recommended for production. Gunicorn (Green Unicorn) is a Python WSGI HTTP Server for Unix and one of the easiest to start with. Make sure to check and make sure its not installed already before installing it:
```
python -m pip install gunicorn==20.1.0

```

11th
Install whitenoise to handle static files
Handling static files in production is a bit more complex than in development. One of the easiest and most popular ways to serve our static files in production is using the WhiteNoise package, which serves them directly from our WSGI Server (Gunicorn). Make sure to check and make sure its not installed already before installing it:
```
python -m pip install whitenoise==6.3.0

```

12th
Update settings.py

A) Add the WhiteNoise to the MIDDLEWARE list right after the SecurityMiddleware.
B) Set the STATIC_ROOT to the directory where the collectstatic management command will collect the static files for deployment.
C) (Optional) Set STATICFILES_STORAGE to CompressedManifestStaticFilesStorage to have compression and caching support.
C.1) Your settings.py file includes a STORAGES dictionary, which is unnecessary if you're using STATICFILES_STORAGE. The STORAGES setting is often used for advanced configurations or to override default storage backends, but it conflicts with STATICFILES_STORAGE if they both attempt to manage static files.

Update your settings.py file by removing the STORAGES dictionary:


```
# settings.py
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'whitenoise.middleware.WhiteNoiseMiddleware',  # <-- Updated!
    ...
]

...
STATIC_ROOT = BASE_DIR / 'staticfiles'  # <-- Updated!

STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'  # <-- Updated!
```

```
# Remove or comment out the STORAGES configuration
# STORAGES = {
#     "default": {
#         "BACKEND": "django.core.files.storage.FileSystemStorage",
#     },
#     "staticfiles": {
#         "BACKEND": "whitenoise.storage.CompressedManifestStaticFilesStorage",
#     },
# }

```

13th
Use WhiteNoise also in development to keep consistent behavior between development and production environments. The easiest way to do that is to add whitenoise.runserver_nostatic to our INSTALLED_APPS right before the built-in staticfiles app:
```
# settings.py
INSTALLED_APPS = [
    ...
    'whitenoise.runserver_nostatic',  # <-- Updated!
    'django.contrib.staticfiles',
    ...
]

```

14th
ALLOWED_HOSTS and CSRF_TRUSTED_ORIGINS
As a security measure, we should set in ALLOWED_HOSTS, a list of host/domain names that our Django website can serve. For development we might include localhost and 127.0.0.1 and for our production we can start with .fly.dev (or the provider’s subdomain you chose) and update for the dedicated URL once your app is deployed to the hosting platform.

CSRF_TRUSTED_ORIGINS should also be defined with a list of origins to perform unsafe requests (e.g. POST). We can set the subdomain https://*.fly.dev (or the provider’s subdomain you chose) until our deployment is done and we have the proper domain for our website.
```
# settings.py
ALLOWED_HOSTS = ['localhost', '127.0.0.1', '.fly.dev']  # <-- Updated!

CSRF_TRUSTED_ORIGINS = ['https://*.fly.dev']  # <-- Updated!
```

Step G
Make sure you have all necessary installed packages tracked and listed in your requirements.txt:
```
pip freeze > requirements.txt
``` 





DEPLOYING TO FLY
Step A
Sign up for a fly account and install fly if you have not already
```
curl -L https://fly.io/install.sh | sh

```

Step B
Configure and launch our app to fly.io
```
fly launch

```

Step C 
During the process, the SECRET_KEY and DATABASE_URL will be automatically set to be used on your production deployment. if you have any other secrets, set them with ```fly secrets set SECRET=VALUE```and replace "SECRET & VALUE" with their respective secret name and value. You can list all your apps secret names:
``` 
fly secrets list
```

```

```

Step X 

```


```

Step X 

```


```

Step X 

```


```

Step X 

```


```

Step X 

```


```

Step X 

```


```

Step X 

```


```

Step X 

```


```

Step X 

```


```

Step X 

```


```

Step Y
Deploy your app
```
fly deploy

```

Step Z
OPen your app
```
fly open
```























