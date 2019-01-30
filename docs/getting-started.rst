Getting Started
===============

*Demo*
If you'd like to check out a demo of DefectDojo before installing it, you can check out on our `Heroku demo site`_.

_Heroku demo site: https://defectdojo.herokuapp.com/

You can log in as an administrator like so:

* admin / defectdojo@demo#appsec

You can also log in as a product owner or non-staff user:

* product_manager / defectdojo@demo#product

*Installation*

Change into the newly created ```django-DefectDojo``` directory:

    ``cd django-DefectDojo/``

There is a script in the main folder called ``setup.bash`` that will allow you to interactively install DefectDojo on any Linux based systems. We do not recommend running DefectDojo as root, but you may do so if you choose.

**You will need:**
* MySQL
* pip

**Recommended**
* virtualenv

1. If you haven't already, run ``mysql_secure_install`` to set a password for your root MySQL user.

2. Create a MySQL user with CREATE privileges, or use root.

**Run the ``setup.bash`` script**
This script will:

1. Install all the operating system packages needed

2. Prompt for database connection information and create the necessary table

3. Install all python packages needed

4. Either ``makemigrations`` and ``migrate`` or ``syncdb`` depending on Django version installed.

5. Provide you with the commands needed to complete the installation

Install Script
~~~~~~~~~~~~~~~

Run the script:

    ``./setup.bash``

During the execution you will be prompted for a few items:

    ``MySQL user (should already exist):``

Enter the user you created or `root` if you used ```mysql_secure_installation```

   ``Password for user:``

Enter the password for the MySQL user you selected.

    ``Database name (should NOT exist):``

Select a name for the DefectDojo database.

**All the packages**

It may take some time for all the `OS` and `python` packages to be installed. As of this writing the packages for this `OS` are:

* gcc
* libssl-dev
* python-dev
* libmysqlclient-dev
* python-pip
* mysql-server
* nodejs-legacy
* npm

The `python` packages are listed in `requirements.txt`.

After all the components have been installed, the `makemigrations` process will prompt you to create a ``superuser``

    ``You have installed Django's auth system, and don't have any superusers defined.
      Would you like to create one now? (yes/no):``

Answer `yes` and follow the prompts, this will be the user you will use to login to DefectDojo.
#. *(OPTIONAL)* If you haven't already, run `mysql_secure_install` to set a password for your root MySQL user.
#. Edit the settings.py file to modify any other settings that you want to
   change, such as your SMTP server information, which we leave off by default.
#. When you are ready to run DefectDojo, run the server with
        ``./run_dojo.bash``

Environment Variables
~~~~~~~~~~~~~~~

All the DefectDojo settings and Django configurations in settings.py can be customized through the use of environment variables or a .env file.

DefectDojo currently uses [django-environ](https://github.com/joke2k/django-environ): which allows you to use _Twelve-factor: https://www.12factor.net/ methodology to configure your Django application with environment variables.

Environment variables can be set from the os environment by setting the following variable as follows: ``export DD_DEBUG=on`` or environment settings can be specified in a file in the dojo/settings/prod.env or overridden by setting DD_ENV_PATH with the name of the env file you wish to use, dev.env for example.

DefectDojo Environment Variables
--------------------------------------

**Required Variables**

The following variables, at a minimum, must be set in order to start DefectDojo.

DD_SECRET_KEY
    A secret key for a particular Django installation. This is used to provide cryptographic signing, and should be set to a unique, unpredictable value.

DD_CREDENTIAL_AES_256_KEY
    AES 256 key for encrypting sensitive data such as passwords in DefectDojo. Set to at least a 256-bit key and should be set to a unique, unpredictable value.

DD_DEBUG
    DefectDojo by default has debug set to off. If testing locally then set DD_DEBUG=on.
    If debug is false then assets such as images will not served. If you want assets to be viewed then set DD_WHITENOISE=on. [WhiteNoise](http://whitenoise.evans.io/en/stable/): allows your web app to serve its own static files, making it a self-contained unit that can be deployed anywhere without relying on nginx, Amazon S3 or any other external service. (Especially useful on Heroku, OpenShift and other PaaS providers.)

DD_ALLOWED_HOSTS
    Hosts/domain names that are valid for this site; If DEBUG is False, default is localhost/127.0.0.1

DD_DATABASE_URL
    Database connections are expressed as URL's conforming to the 12factor approach
    * MySQL: mysql://user:password@host:port/database
    * MySQL example: ``export DD_DATABASE_URL=mysql://root:password@127.0.0.1:3306/dojodb``
    * PostgreSQL: postgres://, pgsql://, psql:// or postgresql://
    * SQLITE: sqlite://

**Sample env file**

prod.env in dojo/settings/prod.env::

    DEBUG=on
    DD_SECRET_KEY=your-secret-key
    DD_CREDENTIAL_AES_256_KEY=your-secret-aes-key
    DATABASE_URL=DD_DATABASE_URL=mysql://root:password@127.0.0.1:3306/dojodb

**Complete DefectDojo Variables List**

DD_DEBUG: If not in os.environ, to enable set DD_DEBUG=on
    Default: False

DD_SECRET_KEY: Raises Django's ImproperlyConfigured exception if SECRET_KEY not in os.environ
    Default: None, must be set by the user

DD_TIME_ZONE: Local time zone for this installation. Choices can be found here: http://en.wikipedia.org/wiki/List_of_tz_zones_by_name
    Default: UTC

DD_LANGUAGE_CODE: Language code for this installation. All choices can be found here: http://www.i18nguy.com/unicode/language-identifiers.html
    Default: en-us

DD_SITE_ID: The ID, as an integer, of the current site in the django_site database table. This is used so that application data can hook into specific sites and a single database can manage content for multiple sites.
    Default: 1

DD_USE_I18N: If you set this to False, Django will make some optimizations so as not to load the internationalization machinery.
    Default: True

DD_USE_L10N: If you set this to False, Django will not format dates, numbers and calendars according to the current locale.
    Default: True

DD_USE_TZ: If you set this to False, Django will not use timezone-aware datetimes.
    Default: True

DD_TEST_RUNNER: The name of the class to use for starting the test suite.
    Default: django.test.runner.DiscoverRunner

DD_DATABASE_URL: Database string expressed as a URL, refer to the documentation above for formatting.
    Default: Must be set by the user

DD_TRACK_MIGRATIONS: Track database migrations through source control rather than managing migrations locally.
    Default: False

DD_MEDIA_ROOT: Absolute filesystem path to the directory that will hold user-uploaded files.
    Default: media

DD_MEDIA_URL:  URL that handles the media served from MEDIA_ROOT. Make sure to use a trailing slash.
    Default: /media/

DD_STATIC_ROOT: Absolute path to the directory static files should be collected to.
    Default: static

DD_STATIC_URL: URL prefix for static files.
    Default: /static/

DD_URL_PREFIX: URL prefix to append, for example DefectDojo is installed in a subdirectory on the server
    Default: None

DD_SECURE_SSL_REDIRECT: If True, the SecurityMiddleware redirects all non-HTTPS requests to HTTPS
    Default: False

DD_SECURE_BROWSER_XSS_FILTER: If True, the SecurityMiddleware sets the X-XSS-Protection: 1;
    Default: False

DD_SESSION_COOKIE_HTTPONLY: Whether to use HTTPOnly flag on the session cookie.
    Default: False

DD_CSRF_COOKIE_HTTPONLY: Whether to use HttpOnly flag on the CSRF cookie.
    Default: True

DD_CSRF_COOKIE_SECURE:  Whether to use a secure cookie for the CSRF cookie.
    Default: False

DD_SECURE_PROXY_SSL_HEADER: Adds an HTTP_X_FORWARDED_PROTO
    Default: False

DD_WKHTMLTOPDF: Path to WKHTMLTOPDF
    Default: /usr/local/bin/wkhtmltopdf

DD_TEAM_NAME: Used in a few places to prefix page headings and in email salutations
    Default: None

DD_FORCE_LOWERCASE_TAGS: Tags that are used in for product, findings etc. and should the ability to force as lowercase.
    Default: True

DD_MAX_TAG_LENGTH: The maximum length of a tag
    Default: 25

DD_ADMINS: DefectDojo admins
    Default: DefectDojo:dojo@localhost,Admin:admin@localhost

DD_DJANGO_ADMIN_ENABLED: Django has a build in admin module (/admin), setting enables or disables this built in Django feature.
    Default: False

DD_WHITENOISE: WhiteNoise allows your web app to serve its own static files
    Default: False

DD_CELERY_BROKER_URL: Celery broker
    Default: sqla+sqlite:///dojo.celerydb.sqlite

DD_CELERY_TASK_IGNORE_RESULT: Ignore celery result
    Default: True

DD_CELERY_RESULT_BACKEND:
    Default: db+sqlite:///dojo.celeryresults.sqlite

DD_CELERY_RESULT_EXPIRES: Seconds to expiration
    Default:86400

DD_CELERY_BEAT_SCHEDULE_FILENAME: Beat filename
    Default: /dojo.celery.beat.db

DD_CELERY_TASK_SERIALIZER: 'pickle', 'json', 'msgpack' or 'yaml'
    Default: pickle


Docker Install
~~~~~~~~~~~~~~~

There are two options for Docker Dojo. The first version is a development / testing version and the second is a docker
compose file with Nginx, MySQL and DefectDojo.

Docker Local Install
*************

*You will need:*

* Latest version of Docker

*Instructions:*

#. Run the docker command to pull the latest version of DefectDojo.
        ``docker run -it -p 8000:8000 appsecpipeline/django-defectdojo bash -c "export LOAD_SAMPLE_DATA=True && bash /opt/django-DefectDojo/docker/docker-startup.bash"``
#. Navigate to: http://localhost:8000 and login with the credentials shown in the terminal.

Docker Compose Install
*************

*You will need:*

* Latest version of Docker
* Latest version Docker Compose

*Instructions:*

#. Clone the `Docker Cloud DefectDojo`_ Repo
        ``git clone https://github.com/aaronweaver/docker-DefectDojo``
#. Change directories into the newly created folder.
        ``cd docker-DefectDojo``
#. Run the setup.bash script which will create a random password for MySQL and Dojo and other setup tasks.
        ``bash setup.bash``
#. Run Docker Compose.
        To run docker-DefectDojo and see the Dojo logs in the terminal, use:
        ``docker-compose up``

        To run docker-DefectDojo and get your terminal prompt back, use:
        ``docker-compose up -d``
#. Navigate to https://localhost and login with the username and password specified in the setup.bash script.
