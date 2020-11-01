Getting Started
===============

Docker Compose Install (recommended)
************************************
* Go to https://github.com/DefectDojo/django-DefectDojo
* Select the appropriate branch you're working on
* Instructions in the [`DOCKER.md`](https://github.com/DefectDojo/django-DefectDojo/blob/master/DOCKER.md) file at the root of the repository.

Kubernetes
**********
* Go to https://github.com/DefectDojo/django-DefectDojo
* Select the appropriate branch you're working on
* Instructions in the [`KUBERNETES.md`](https://github.com/DefectDojo/django-DefectDojo/blob/master/KUBERNETES.md) file at the root of the repository.

Setup.bash Install (no longer maintained)
*****************************************
.. warning::
   This installation method will be EOL on 2020-12-31

* Go to https://github.com/DefectDojo/django-DefectDojo
* Select the appropriate branch you're working on
* Under "Installation Options" click "Setup.bash"
* Follow the instructions

Customizing settings
********************
* You can add / change settings by adding a `local_settings.py` file to the `dojo/docker/extra_settings/` folder (release mode)
* For more info on custom settings and use of custom settings during development, please see: [settings.py documentation](https://github.com/DefectDojo/django-DefectDojo/blob/master/dojo/settings/settings.py) and [extra settings](https://github.com/DefectDojo/django-DefectDojo/blob/master/docker/extra_settings/README.md)
