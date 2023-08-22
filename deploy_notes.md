# Deploy Django to Heroku notes

## Getting setup locally

Your git manager should do these steps and introduce these
changes to upstream. Collaborates should pull the changes
once finished.

* ensure that you are in your virtual enviroment
* install the [gunicorn](https://gunicorn.org/) package with `pip3 install gunicorn`
* install [whitenoise](https://whitenoise.readthedocs.io/en/stable/django.html) with `pip3 install whitenoise`	
* install `pip3 install dj-database-url`
* setup [parts 1 + 2](https://whitenoise.readthedocs.io/en/stable/django.html)
* freeze your requirements `pip3 freeze > requirements.txt`
* touch `Procfile` and add the following:
```sh
web: gunicorn yourappname.wsgi
```
* add `venv` and `__pycache__/` to your `.gitignore`
* set/add the follwing in `settings.py` ([here](https://github.com/ga-sei-lessons/620-django-deploy-test/blob/main/mysite/settings.py) is an example to reference if needed):
```python
import dj_database_url
PRODUCTION = 'PRODUCTION' in os.environ
...
SECRET_KEY = 're94qkxft_#0mq@1j-#t%g5i9^uvh@7y6z+(i0ku#q66ma*uar'
if PRODUCTION:
    SECRET_KEY = os.environ["SECRET_KEY"]
...
DEBUG = True
if PRODUCTION:
    DEBUG = False
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
        'NAME': '< your db name >',
    }
}

if PRODUCTION:
    db_from_env = dj_database_url.config(conn_max_age=600)
    DATABASES['default'].update(db_from_env)
...
STATIC_URL = '/static/'
STATIC_ROOT = BASE_DIR + "/staticfiles"
STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
```
* git add, commit and push!

## Config on heroku

* config eco dynos for postgres dbs
* setup new app on heroku
* install the [heroku cli](https://devcenter.heroku.com/articles/heroku-cli)
* `heroku login` -- login via browser
* `heroku git:remote -a < your heroku app name >` -- connect to app in cli
* `heroku addons:create heroku-postgresql:mini` -- install postgres package
* `heroku logs --tail` -- monitor deploy's terminal (open separate tab/pane)
* `heroku config:set PRODUCTION=True SECRET_KEY="some string"` -- set config vars
* `heroku run python manage.py migrate` -- migrate db
* `heroku pg:psql` -- check your prod db's shell for correct migration
* `heroku run python manage.py collectstatic` -- config static assets if needed (if your static assets are borked)

## heroku cheatsheet:

```
# check config vars
heroku config
# add a config var
heroku config:set KEY=value KEY_TWO=value
# run a command on the cloud
heroku run < my command >
# enter the psql terminal on the cloud
heroku pg:psql
# monitor the output of your server's console
heroku logs --tail
# if you need to add something from your .env file
heroku config:set VAR_NAME=value
# migrate your db with sequelize (after pushing changes to your migration files)
heroku run db:migrate 
# force logging in to heroku
heroku login
```


