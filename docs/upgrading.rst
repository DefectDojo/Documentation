Upgrading
=========

The easiest way to upgrade to a new version of DefectDojo is to pull from Github.  Assuming the source code lives in a
directory named `defect-dojo` you can complete the following steps to upgrade to the latest DefectDojo release.::

    cd defect-dojo
    git checkout master
    git pull
    pip freeze > pip_frozen.txt
    pip install -r pip_frozen.txt --upgrade
    ./manage.py makemigrations dojo
    ./manage.py makemigrations
    ./manage.py migrate

Because yarn assets change from time to time, it is always a good idea to re-install them and collect the static
resources. ::

    cd defect-dojo
    cd components
    yarn
    cd ..

At this point yarn may ask you to select from different versions of packages, choose the latest on each.

Next you can run: ::

    ./manage.py collectstatic --noinput

If you are in your production system, you will need to restart gunicorn and celery to make sure the latest code is
being used by both.

FAQ
---

**Celery Error:**

If you have an issue starting Django with the error: TypeError: config_from_object() got an unexpected keyword argument 'namespace'

Upgrade Celery to the latest version:

    ``pip install --upgrade celery``

Upgrading to DefectDojo Version 1.9.0
-------------------------------------
**What's New:**
- See release notes: https://github.com/DefectDojo/django-DefectDojo/releases
- Search index tweaking index rebuild after upgrade:

This requires a (one-time) rebuild of the Django-Watson search index. Execute the django command from the defect dojo installation directory:

`./manage.py buildwatson`

If you're using docker:

`docker-compose exec uwsgi ./manage.py buildwatson`

This can take a while depending on your hardware and the number of findings in your instance.



Upgrading to DefectDojo Version 1.8.0
-------------------------------------
**What's New:**
- See release notes: https://github.com/DefectDojo/django-DefectDojo/releases
- Improved search, which requires an index rebuild (https://github.com/DefectDojo/django-DefectDojo/pull/2861)

This requires a (one-time) rebuild of the Django-Watson search index. Execute the django command from the defect dojo installation directory:

`./manage.py buildwatson`

If you're using docker:

`docker-compose exec uwsgi ./manage.py buildwatson`

This can take a while depending on your hardware and the number of findings in your instance.

Upgrading to DefectDojo Version 1.7.0 
-------------------------------------

**What's New:**

 * Updated search, you can now search for CVE-XXXX-YYYY
 * Updated search index, fields added to index: 'id', 'title', 'cve', 'url', 'severity', 'description', 'mitigation', 'impact', 'steps_to_reproduce', 'severity_justification', 'references', 'sourcefilepath', 'sourcefile', 'hash_code', 'file_path', 'component_name', 'component_version', 'unique_id_from_tool'

This requires a (one-time) rebuild of the Django-Watson search index. Execute the django command from the defect dojo installation directory:

`./manage.py buildwatson dojo.Finding`

If you're using docker:

`docker-compose exec uwsgi ./manage.py buildwatson dojo.Finding`

Upgrading to DefectDojo Version 1.5.0
-------------------------------------

**What's New:**

 * Updated UI with a new DefectDojo logo, default colors and CSS.
 * Updated Product views with tabs for Product Overview, Metrics, Engagements, Endpoints, Benchmarks (ASVS), and Settings to make it easier to navigate and manage your products.
 * New Product Information fields: Regulations, Criticality, Platform, Lifecycle, Origin, User Records, Revenue, External Audience, Internet Accessible
 * Languages pie chart on product overview, only supported through the API and Django admin, integrates with cloc analyzer
 * New Engagement type of CI/CD to support continual testing
 * Engagement shortcuts and ability to import findings and auto-create an engagement
 * Engagement labels for overdue, no tests and findings
 * New Contextual menus throughout DefectDojo and shortcuts to new findings and critical findings
 * Ability to merge a finding into a parent finding and either inactivate or delete the merged findings.
 * Report improvements and styling adjustment with the default option of HTML reports
 * SLA for remediation of severities based on finding criticality, for example critical findings remediated within 7 days. Configurable in System Settings.
 * Engagement Auto-Close Days in System Settings. Automatically close an engagement if open past the end date.
 * Ability to apply remediation advice based on CWE. For example XSS can be configured as a template so that it's consistent across all findings. Enabled in system settings.
 * Finding confidence field supported from scanners. First implementation in the Burp importer.
 * Goast importer for static analysis of Golang products
 * Celery status check on System Settings
 * Beta rules framework release for modifying findings on the fly
 * DefectDojo 2.0 API with Swagger support
 * Created and Modified fields on all major tables
 * Various bug fixes reported on Github

**Upgrading to 1.5.0 requirements:**

1. Back up your database first, ideally take the backup from production and test the upgrade on a staging server.

2. Edit the settings.py file which can be found in ``django-DefectDojo/dojo/settings/settings.py``. Copy in the rest framework configuration after the CSRF_COOKIE_SECURE = True::

    REST_FRAMEWORK = {
        'DEFAULT_AUTHENTICATION_CLASSES': (
            'rest_framework.authentication.TokenAuthentication',
            'rest_framework.authentication.BasicAuthentication',
        ),
        'DEFAULT_PERMISSION_CLASSES': (
            'rest_framework.permissions.DjangoModelPermissions',
        ),
        'DEFAULT_RENDERER_CLASSES': (
            'rest_framework.renderers.JSONRenderer',
        ),
        'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.LimitOffsetPagination',
        'PAGE_SIZE': 25
    }

Navigate to: LOGIN_EXEMPT_URLS and add the following after r'^%sfinding/image/(?P<token>[^/]+)$' % URL_PREFIX::

    r'^%sfinding/image/(?P<token>[^/]+)$' % URL_PREFIX,
    r'^%sapi/v2/' % URL_PREFIX,

Navigate to: INSTALLED_APPS and add the following after: 'multiselectfield',::

    'multiselectfield',
    'rest_framework',
    'rest_framework.authtoken',
    'rest_framework_swagger',
    'dbbackup',

Navigate to: 	CELERY_TASK_IGNORE_RESULT = True and add the following after CELERY_TASK_IGNORE_RESULT line::

    CELERY_RESULT_BACKEND = 'db+sqlite:///dojo.celeryresults.sqlite'

Save your modified settings file. For reference the modified file should look like the new 1.5.0 [settings](https://github.com/DefectDojo/django-DefectDojo/blob/master/dojo/settings/settings.dist.py) file, minus the environmental configurations. As an alternative this file can be used and the enviromental configurations from you environment can be copied into this file.

3. Activate your virtual environment and then upgrade the requirements:

``pip install -r requirements.txt --upgrade``

4. Upgrade the database::

    ./manage.py makemigrations
    ./manage.py migrate

5. Collect the static files (Javascript, Images, CSS)::

    ./manage.py collectstatic --noinput

6. Complete

Upgrading to DefectDojo Version 1.3.1
-------------------------------------

**What's New:**

 * New importers for Contrast, Nikto and TruffleHog (finding secrets in git repos).
 * Improved merging of findings for dynamic and static importers
 * Markdown support for findings
 * HTML report improvements including support of Markdown.
 * System settings Celery status page to assist in debugging if Celery is functional.

**Upgrading to 1.3.1 requires:**

1.  pip install markdown
    pip install pandas

2.  ./manage.py makemigrations
    ./manage.py migrate

3. ./manage.py collectstatic --noinput

4. Complete

Upgrading to DefectDojo Version 1.2.9
-------------------------------------

**What's New:**
New feature: Benchmarks (OWASP ASVS)

**Upgrading to 1.2.9 requires:**

1.  ./manage.py makemigrations
    ./manage.py migrate
    ./manage.py loaddata dojo/fixtures/benchmark_type.json
    ./manage.py loaddata dojo/fixtures/benchmark_category.json
    ./manage.py loaddata dojo/fixtures/benchmark_requirement.json

2. ./manage.py collectstatic --noinput

3. Complete

Upgrading to DefectDojo Version 1.2.8
-------------------------------------

New feature: Product Grading (Overall Product Health)
Upgrading to 1.2.8 requires:

1.  ./manage.py makemigrations
    ./manage.py migrate
    ./manage.py system_settings

2. ./manage.py collectstatic --noinput

3. pip install asteval

4. pip install --upgrade celery

5. Complete

Upgrading to DefectDojo Version 1.2.4
-------------------------------------

Upgrading to 1.2.4 requires:

1.  ./manage.py makemigrations
    ./manage.py migrate
    ./manage.py loaddata dojo/fixtures/objects_review.json

Upgrading to DefectDojo Version 1.2.3
-------------------------------------

Upgrading to 1.2.3 requires:

1.  ./manage.py makemigrations
    ./manage.py migrate
    ./manage.py loaddata dojo/fixtures/language_type.json

2. Currently languages and technologies can be updated via the API or in the admin section of Django.

July 6th 2017 - New location for system settings
------------------------------------------------

Pull request #313 moves a number of system settings previously located in the application's settings.py
to a model that can be used and changed within the web application under "Configuration -> System Settings".

If you're using a custom ``URL_PREFIX`` you will need to set this in the model after upgrading by
editing ``dojo/fixtures/system_settings.json`` and setting your URL prefix in the ``url_prefix`` value there.
Then issue the command ``./manage.py loaddata system_settings.json`` to load your settings into the database.

If you're not using a custom ``URL_PREFIX``, after upgrading simply go to the System Settings page and review
which values you want to set for each setting, as they're not automatically migrated from settings.py.

If you like you can then remove the following settings from settings.py to avoid confusion:

* ``ENABLE_DEDUPLICATION``
* ``ENABLE_JIRA``
* ``S_FINDING_SEVERITY_NAMING``
* ``URL_PREFIX``
* ``TIME_ZONE``
* ``TEAM_NAME``

Upgrading to DefectDojo Version 1.2.2
-------------------------------------

Upgrading to 1.2.2 requires:

1. Copying settings.py to the settings/ folder.

2. If you have supervisor scripts change DJANGO_SETTINGS_MODULE=dojo.settings.settings

Upgrading to Django 1.1.5
-------------------------
If you are upgrading an existing version of DefectDojo, you will need to run the following commands manually:

#. First install Yarn.
   Follow the instructions based on your OS: https://yarnpkg.com/lang/en/docs/install/

#. The following must be removed/commented out from ``settings.py``: ::

    'djangobower.finders.BowerFinder',

    From the line that contains:
    # where should bower install components
    ...

    To the end of the bower declarations
      'justgage'
    )

#. The following needs to be updated in ``settings.py``: ::

    STATICFILES_DIRS = (
        # Put strings here, like "/home/html/static" or "C:/www/django/static".
        # Always use forward slashes, even on Windows.
        # Don't forget to use absolute paths, not relative paths.
        os.path.dirname(DOJO_ROOT) + "/components/yarn_components",
    )

Upgrading to Django 1.11
------------------------

Pull request #300 makes DefectDojo Django 1.11 ready. A fresh install of DefectDojo can be done with the setup.bash script included - no special steps are required.

If you are upgrading an existing installation of DefectDojo, you will need to run the following commands manually: ::

    pip install django-tastypie --upgrade
    pip install django-tastypie-swagger --upgrade
    pip install django-filter --upgrade
    pip install django-watson --upgrade
    pip install django-polymorphic --upgrade
    pip install django --upgrade
    pip install pillow --upgrade
    ./manage.py makemigrations
    ./manage.py migrate

The following must be removed/commented out from settings.py: ::

    TEMPLATE_DIRS
    TEMPLATE_DEBUG
    TEMPLATE_LOADERS
    TEMPLATE_CONTEXT_PROCESSORS

The following needs to be added to settings.py: ::

    TEMPLATES  = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
    ]

Once all these steps are completed your installation of DefectDojo will be running under Django 1.11
