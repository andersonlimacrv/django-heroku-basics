# Hosting a Django Project on Heroku

This guide will walk you through the steps to host your Django project on Heroku while maintaining an organized and elegant structure.

## Project Setup

1. Create a New Project Directory
    ```shell
    mkdir directory_name
    cd directory_name
    ```

2. Set Up and Activate a Virtual Environment
    ```shell
    virtualenv -p python3 .vEnv
    source .vEnv/bin/activate
    ```

3. Install Django
    ```shell
    pip install django
    ```

4. Create the Django Project
    ```shell
    django-admin startproject myproject
    ```

## Git Setup

1. Initialize Git Repository
    ```shell
    git init
    ```

2. Create a `.gitignore` file and add the following content:
    ```
    # IDE
    .idea
    # SQLite database
    *.sqlite3
    # Virtual environment
    .vEnv
    *pyc
    ```

3. Commit Initial Changes
    ```shell
    git add .
    git commit -m 'First commit'
    ```

## Environment Configuration

1. Install `python-decouple`
    ```shell
    pip install python-decouple
    ```

2. Create an `.env` file at the root and add the following variables:
    ```
    SECRET_KEY=Your$eCretKeyHere
    DEBUG=True
    ```

3. In `settings.py`, use `decouple`:
    ```python
    from decouple import config

    SECRET_KEY = config('SECRET_KEY')
    DEBUG = config('DEBUG', default=False, cast=bool)
    ```

## Database Configuration

1. Install `dj-database-url`
    ```shell
    pip install dj-database-url
    ```

2. Configure the Database in `settings.py`:
    ```python
    from dj_database_url import parse as dburl

    default_dburl = 'sqlite:///' + os.path.join(BASE_DIR, 'db.sqlite3')

    DATABASES = {
        'default': config('DATABASE_URL', default=default_dburl, cast=dburl),
    }
    ```

## Static Files

1. Install `dj-static`
    ```shell
    pip install dj-static
    ```

2. In `wsgi.py`, add the following:
    ```python
    from dj_static import Cling
    application = Cling(get_wsgi_application())
    ```

3. In `settings.py`, set `STATIC_ROOT`:
    ```python
    STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
    ```

## Requirements

1. Create `requirements-dev.txt`
    ```shell
    pip freeze > requirements-dev.txt
    ```

2. Create `requirements.txt` and add references to previous files, plus two more requirements:
    ```
    -r requirements-dev.txt
    gunicorn
    psycopg2
    ```

## Procfile

Create a `Procfile` with the following content:
```
web: gunicorn project.wsgi
```
For more details, refer to:
[Django Gunicorn Deployment](https://docs.djangoproject.com/en/2.2/howto/deployment/wsgi/gunicorn/)
[Heroku Django Configuration](https://devcenter.heroku.com/articles/django-app-configuration)

## Runtime Configuration

Create a `runtime.txt` file and specify the Python version:
```
python-3.7.3
```

## Heroku Deployment

1. Install Heroku CLI Tools (if not already installed)
   [Heroku CLI Installation Guide](http://bit.ly/2jCgJYW)

2. Create the Heroku App
    ```shell
    heroku apps:create app-name
    ```
    You can also create the app through the Heroku dashboard.

3. Set Allowed Hosts
    Add your app's domain to the `ALLOWED_HOSTS` directive in `settings.py`.

4. Install Heroku Config Plugin
    ```shell
    heroku plugins:install heroku-config
    ```

5. Push Environment Configurations to Heroku
    ```shell
    heroku config:push -a
    ```

6. Deploy the App
    ```shell
    git add .
    git commit -m 'Configuring the app'
    git push heroku master
    ```

7. Database Migration
    ```shell
    heroku run python3 manage.py migrate
    ```

8. Create Django Admin User
    ```shell
    heroku run python3 manage.py createsuperuser
    ```

## Extras

- Disable Collectstatic (if needed)
    ```shell
    heroku config:set DISABLE_COLLECTSTATIC=1
    ```

- Adjust Web Concurrency
    ```python
    WEB_CONCURRENCY = 3
    ```

- Change Specific Configuration
    ```shell
    heroku config:set DEBUG=True
    ```
Refer to the Django and Heroku documentation for further details:

Refer to the Django and Heroku documentation for further details:
- [Django Deployment with Gunicorn](https://docs.djangoproject.com/en/3.2/howto/deployment/wsgi/gunicorn/)
- [Heroku Django App Configuration](https://devcenter.heroku.com/articles/django-app-configuration)
