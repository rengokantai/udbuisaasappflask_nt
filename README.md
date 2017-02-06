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

###42 
Install Click first.then  
cli/commands/cmd_cov.py
```
import subprocess

import click


@click.command()
@click.argument('path', default='snakeeyes')
def cli(path):
    """
    Run a test coverage report.

    :param path: Test coverage path
    :return: Subprocess call result
    """
    cmd = 'py.test --cov-report term-missing --cov {0}'.format(path)
    return subprocess.call(cmd, shell=True)
```
compile and eval function
```
#!/usr/bin/python
# -*- coding: utf-8 -*-


def get_command(self, ctx, name):
    """
        Get a specific command by looking up the module.

        :param ctx: Click context
        :param name: Command name
        :return: Module's cli function
        """

    ns = {}

    filename = os.path.join(cmd_folder, cmd_prefix + name + '.py')    

    with open(filename) as f:
        code = compile(f.read(), filename, 'exec')      #compile function
        eval(code, ns, ns)                              #eval function

    return ns['cli']
```


###43
```
docker-compose exec website snakeeyes
```

##11
###51
by default scp cannot copy hidden files. [stackoverflow](http://serverfault.com/questions/21580/how-to-make-scp-copy-hidden-files)
```
scp -rp src/. user@server:dest/
```
Or better, use rsync
```
rsync -av src/ user@server:dest/
```

###54
####02:08
snakeeyes/blueprints/contact/templates/contact/index.html
```
{% import 'macros/form.html' as f with context %}
```
call this function
```
<div class="row">
    <div class="col-md-8 col-md-offset-2 well">
      {% call f.form_tag('contact.index') %}
        <legend>We're here to answer your questions</legend>

        {% call f.form_group(form.email, css_class='margin-bottom',
                             placeholder='E-mail address') %}
        {% endcall %}

        {% call f.form_group(form.message, css_class='margin-bottom',
                             placeholder='The more we know, the easier it will be to help you',
                             rows='12') %}
        {% endcall %}

        <button type="submit" class="btn btn-primary">Send e-mail</button>
      {% endcall %}
    </div>
  </div>
  ```
