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
```
web: gunicorn yourappname.wsgi
```
* add `venv` and `__pycache__/` to your `.gitignore`
* git add, commit a push!


## Config on heroku

* setup new app on heroku
* install the [heroku cli](https://devcenter.heroku.com/articles/heroku-cli)
* `heroku login` -- login via browser
* `heroku run python manage.py collectstatic`
