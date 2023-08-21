# Deploy Django to Heroku notes

## Getting setup locally

Your git manager should do these steps and introduce these
changes to upstream. Collaborates should pull the changes
once finished.

* ensure that you are in your virtual enviroment
* install the [gunicorn](https://gunicorn.org/) package with `pip3 install gunicorn`
* install [whitenoise](https://whitenoise.readthedocs.io/en/stable/django.html) with `pip3 install whitenoise`	
* setup [parts 1 + 2](https://whitenoise.readthedocs.io/en/stable/django.html) and then run `heroku run python manage.py collectstatic`
* freeze your requirements `pip3 freeze > requirements.txt`
* touch `Profile` and add the following:
```sh
web: gunicorn yourappname.wsgi
```
* add `venv` and `__pycache__/` to your `.gitignore`
* set/add the follwing in `settings.py`:
```python
import dj_database_url
PRODUCTION = PRODUCTION in os.environ
...
if PRODUCTION:
    DEBUG = False
else:
    DEBUG = True
...
ALLOWED_HOSTS = ['*',]
...
INSTALLED_APPS = [
...
    'whitenoise.runserver_nostatic'
]
MIDDLEWARE = [
	# this middleware should be on top of the whitenoise middleware
    'django.middleware.security.SecurityMiddleware',
    'whitenoise.middleware.WhiteNoiseMiddleware',
...
]
...
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'polls',
    }
}

if PRODUCTION:
    db_from_env = dj_database_url.config(conn_max_age=600)
    DATABASES['default'].update(db_from_env)
...
STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
```
* git add, commit a push!

## Config on heroku

* config eco dynos for postgres dbs
* setup new app on heroku
* install the [heroku cli](https://devcenter.heroku.com/articles/heroku-cli)
* `heroku login` -- login via browser
* `heroku git:remote -a < your heroku app name >` -- connect to app in cli
* `heroku addons:create heroku-postgresql:mini` -- install postgres package
* `heroku config:set PGSSLMODE=no-verify` -- disable db ssl verifactaion
* `heroku logs --tail` -- monitor deploy's terminal (open separate tab/pane)
* `heroku run python manage.py collectstatic` -- config static assets
* `heroku run python manage.py migrate` -- migrate db 

