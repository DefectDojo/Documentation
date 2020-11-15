Running in Production
=====================

Improving your docker-compose performance
-----------------------------------------

Uwsgi and celery logs
^^^^^^^^^^^^^^^^^^^^^
You may want to log messages produced by your celery beat and worker as well as uwsgi to json, to ease log processing as you ship logs to your central logging platform.

Setting the ``DD_LOGGING_HANDLER`` environment variable to ``json_console`` for these containers will have them log in JSON format.

Database
^^^^^^^^
Run your database elsewhere. Tweak your docker-compose configuration to that effect.

Key processes
^^^^^^^^^^^^^
Per https://github.com/DefectDojo/django-DefectDojo/pull/2813, it is now easy to improve the uWSGI and celery worker performance.

uWSGI
"""""
By default (except in ``ptvsd`` mode for debug purposes), uWSGI will handle 4 concurrent connections.

Based on your resource settings, you can tweak:

* ``DD_UWSGI_NUM_OF_PROCESSES`` for the number of spawned processes. (default 2)
* ``DD_UWSGI_NUM_OF_THREADS`` for the number of threads in these processes. (default 2)

For example, you may have 4 processes with 6 threads each, yielding 24 concurrent connections.

Celery worker
"""""""""""""
By default, a single mono-process celery worker is spawned. This is fine until you start having many findings, and when async operations like deduplication start to kick in. Eventually, it will starve your resources and crawl to a halt, while operations continue to queue up.

The following variables will help a lot, while keeping a single celery worker container.

* ``DD_CELERY_WORKER_POOL_TYPE`` will let you switch to ``prefork``. (default ``solo``)

As you've enabled `prefork`, the following variables have to be used. The default are working fairly well, see the Dockerfile.django for in-file references.

* ``DD_CELERY_WORKER_AUTOSCALE_MIN`` defaults to 2.
* ``DD_CELERY_WORKER_AUTOSCALE_MAX`` defaults to 8.
* ``DD_CELERY_WORKER_CONCURRENCY`` defaults to 8.
* ``DD_CELERY_WORKER_PREFETCH_MULTIPLIER`` defaults to 128.

You can execute the following command to see the configuration:

``docker-compose exec celerybeat bash -c "celery -A dojo inspect stats"`` and see what is in effect.

Production with setup.bash
==========================

.. warning::
   From this point down, this page is slated to get a revamp

This guide will walk you through how to setup DefectDojo for running in production using Ubuntu 16.04, nginx, and uwsgi.

**Install, Setup, and Activate Virtualenv**

Assumes running as root or using sudo command for the below.

.. code-block:: console

  pip install virtualenv

  cd /opt

  virtualenv dojo

  cd /opt/dojo

  git clone https://github.com/DefectDojo/django-DefectDojo.git

  useradd -m dojo

  chown -R dojo /opt/dojo

  source ./bin/activate

**Install Dojo**

.. warning::
   The setup.bash installation method will be EOL on 2020-12-31

.. code-block:: console

  cd django-DefectDojo/setup

  ./setup.bash

**Install Uwsgi**

.. code-block:: console

  pip install uwsgi

**Install WKHTML**

from inside the django-DefectDojo/ directory execute:

.. code-block:: console

  ./reports.sh

**Disable Debugging**

Using the text-editor of your choice, change ``DEBUG`` in django-DefectDojo/dojo/settings/settings.py to:

.. code-block:: console

  `DEBUG = False`

**Configure external database**

If you host your DefectDojo into AWS and you decide to use their managed database service (AWS RDS), you will have to do the following configuration updates:

1) `Download the root certificate <https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/UsingWithRDS.SSL.html>`_ to encrypt traffic between DefectDojo and the database
2) Update your Dockerfile to add the SSL certificate to the container

.. code-block:: console
   :caption: Dockerfile.django

   COPY rds-ca-2019-root.pem /etc/ssl/certs/rds-ca-2019-root.pem

3) Update Django settings to use encrypted connection to the database (Changes highlighted below)

.. code-block:: python
   :caption: dojo/settings/settings.dist.py
   :emphasize-lines: 4-6

       DATABASES = {
           'default': env.db('DD_DATABASE_URL')
       }
       DATABASES['default']['OPTIONS'] = {
       'ssl': {'ca': '/etc/ssl/certs/rds-ca-2019-root.pem'}
       }
   else:
       DATABASES = {
           'default': {

4) Update the environment variables for the database connection: *DD_DATABASE_URL* or *DD_DATABASE_HOST*, *DD_DATABASE_PORT*, *DD_DATABASE_NAME*, *DD_DATABASE_USER* and *DD_DATABASE_PASSWORD*.

Note: This configuration can be adapted to other cloud providers.

**Start Celery and Beats**

From inside the django-DefectDojo/ directory execute:

.. code-block:: console

  celery -A dojo worker -l info --concurrency 3

  celery beat -A dojo -l info

It is recommended that you daemonized both these processes with the sample configurations found `here`_ and `here.`_

.. _here: https://github.com/celery/celery/blob/3.1/extra/supervisord/celeryd.conf
.. _here.: https://github.com/celery/celery/blob/3.1/extra/supervisord/celerybeat.conf

However, for a quick setup you can use the following to run both in the background

.. code-block:: console

  celery -A dojo worker -l info --concurrency 3 &

  celery beat -A dojo -l info &

**Start Uwsgi**

From inside the django-DefectDojo/ directory execute:

.. code-block:: console

  uwsgi --socket :8001 --wsgi-file wsgi.py --workers 7

It is recommended that you use an Upstart job or a @restart cron job to launch uwsgi on reboot. However, if you’re in a hurry you can use the following to run it in the background:

.. code-block:: console

  uwsgi --socket :8001 --wsgi-file wsgi.py --workers 7 &

**Making Defect Dojo start on boot**

Below we configure service files for systemd.  The commands follow, the config files are below the Nginx in the next section.

.. code-block:: shell-session

  $ cd /etc/systemd/system/
  $ sudo vi dojo.service
  [contents below]

  $ sudo systemctl enable dojo
  $ sudo systemctl start dojo
  $ sudo systemctl status dojo
  [ensure it launched OK]

  $ sudo vi celery-worker.service
  [contents below]

  $ sudo systemctl enable celery-worker
  $ sudo systemctl start celery-worker
  $ sudo systemctl status celery-worker
  [ensure it launched OK]

  $ sudo vi celery-beat.service
  [contents below]

  $ sudo systemctl enable celery-beat
  $ sudo systemctl start celery-beat
  $ sudo systemctl status celery-beat
  [ensure it launched OK]


*NGINX Configuration*

Everyone feels a little differently about nginx settings, so here are the barebones to add your to your nginx configuration to proxy uwsgi. Make sure to modify the filesystem paths if needed:

.. code-block:: nginx

  upstream django {
    server 127.0.0.1:8001;
  }

  server {
    listen 80;
    return 301 https://$host$request_uri;
  }

  server {
    listen 443;
    server_name <YOUR_SERVER_NAME>;

    client_max_body_size 500m; # To accommodate large scan files

    ssl_certificate           <PATH_TO_CRT>;
    ssl_certificate_key       <PATH_TO_KEY>;

    ssl on;

    <YOUR_SSL_SETTINGS> # ciphers, options, logging, etc

    location /static/ {
        alias   <PATH_TO_DOJO>/django-DefectDojo/static/;
    }

    location /media/ {
        alias   <PATH_TO_DOJO>/django-DefectDojo/media/;
    }

    location / {
        uwsgi_pass django;
        include     <PATH_TO_DOJO>/django-DefectDojo/wsgi_params;
    }
  }

*Systemd Configuration Files*

dojo.service

.. code-block:: ini

  [Unit]
  Description=uWSGI instance to serve DefectDojo
  Requires=nginx.service mysql.service
  Before=nginx.service
  After=mysql.service

  [Service]
  ExecStart=/bin/bash -c 'su - dojo -c "cd /opt/dojo/django-DefectDojo && source ../bin/activate && uwsgi --socket :8001 --wsgi-file wsgi.py --workers 7"'
  Restart=always
  RestartSec=3
  #StandardOutput=syslog
  #StandardError=syslog
  SyslogIdentifier=dojo

  [Install]
  WantedBy=multi-user.target

celery-worker.service

.. code-block:: ini

  [Unit]
  Description=celery workers for DefectDojo
  Requires=dojo.service
  After=dojo.service

  [Service]
  ExecStart=/bin/bash -c 'su - dojo -c "cd /opt/dojo/django-DefectDojo && source ../bin/activate && celery -A dojo worker -l info --concurrency 3"'
  Restart=always
  RestartSec=3
  #StandardOutput=syslog
  #StandardError=syslog
  SyslogIdentifier=celeryworker

  [Install]
  WantedBy=multi-user.target

celery-beat.service

.. code-block:: ini

  [Unit]
  Description=celery beat for DefectDojo
  Requires=dojo.service
  After=dojo.service

  [Service]
  ExecStart=/bin/bash -c 'su - dojo -c "cd /opt/dojo/django-DefectDojo && source ../bin/activate && celery beat -A dojo -l info"'
  Restart=always
  RestartSec=3
  #StandardOutput=syslog
  #StandardError=syslog
  SyslogIdentifier=celerybeat

  [Install]
  WantedBy=multi-user.target


*That's it!*

*Monitoring*

To expose Django statistics for Prometheus, using the text-editor of your choice, change ``DJANGO_METRICS_ENABLED`` to True in django-DefectDojo/dojo/settings/settings.py to:

.. code-block:: console

  `DJANGO_METRICS_ENABLED = True`

Or export ``DD_DJANGO_METRICS_ENABLED`` with the same value.

Prometheus endpoint than is available under the path: ``http://dd_server/django_metrics/metrics``
