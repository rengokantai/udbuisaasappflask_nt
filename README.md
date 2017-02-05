# udbuisaasappflask_nt


For docker-compose installation: OSX
```
curl -L "https://github.com/docker/compose/releases/download/1.10.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
```
ubuntu
```
curl -L "https://github.com/docker/compose/releases/download/1.10.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/bin/docker-compose
chmod +x /usr/bin/docker-compose
```

docker-compose.yml
```
version: '2'

services:
  website:
    build: .
    command: >
      gunicorn -b 0.0.0.0:8000
        --access-logfile -
        --reload
        "snakeeyes.app:create_app()"
    environment:
      PYTHONUNBUFFERED: 'true'
    volumes:
      - '.:/snakeeyes'
    ports:
      - '8000:8000'
```

Dockerfile
```
FROM python:2.7-slim
MAINTAINER Nick Janetakis <nick.janetakis@gmail.com>

ENV INSTALL_PATH /snakeeyes
RUN mkdir -p $INSTALL_PATH

WORKDIR $INSTALL_PATH

COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt

COPY . .

CMD gunicorn -b 0.0.0.0:8000 --access-logfile - "snakeeyes.app:create_app()"
```

###24
cleanup
```
docker-compose rm -f
docker rmi -f $(docker images -qf dangling=true)
```

###35
```
docker-compose exec website py.test snakeeyes/tests
```
###36
```
docker-compose exec website py.test --cov-report term-missing --cov snakeeyes
```
test and original file:

snakeeyes/blueprints/page/views.py
```
from flask import Blueprint, render_template

page = Blueprint('page', __name__, template_folder='templates')


@page.route('/')
def home():
    return render_template('page/home.html')


@page.route('/terms')
def terms():
    return render_template('page/terms.html')


@page.route('/privacy')
def privacy():
    return render_template('page/privacy.html')
```
snakeeyes/tests/page/test_views.py
```
class TestPage(object):
    def test_home_page(self, client):
        """ Home page should respond with a success 200. """
        response = client.get(url_for('page.home'))
        assert response.status_code == 200

    def test_terms_page(self, client):
        """ Terms page should respond with a success 200. """
        response = client.get(url_for('page.terms'))
        assert response.status_code == 200

    def test_privacy_page(self, client):
        """ Privacy page should respond with a success 200. """
        response = client.get(url_for('page.privacy'))
        assert response.status_code == 200
```

###37
```
docker-compose exec website flake8 .
docker-compose exec website flake8 . --exclude __init__.py
```

