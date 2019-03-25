DefectDojo's Documentation
==========================

.. image:: /_static/dashboard.png

**About DefectDojo**

*What is DefectDojo?*

.. image:: /_static/bug-2x.png

`DefectDojo` is a security tool that automates application security vulnerability management. `DefectDojo` streamlines the application security testing process by offering features such as importing third party security findings, merging and de-duping, integration with Jira, templating, report generation and security metrics.

*What does DefectDojo do?*

.. image:: /_static/graph-2x.png

While traceability and metrics are the ultimate end goal, DefectDojo is a bug tracker at its core. Taking advantage of DefectDojo's Product:Engagement model, enables traceability among multiple projects and test cycles, and allows for fine-grained reporting.

*How does DefectDojo work?*

.. image:: /_static/key-2x.png

DefectDojo is based on a model that allows the ultimate flexibility in your test tracking needs.

* Working in DefectDojo starts with a ``Product Type``.
* Each Product Type can have one of more ``Products``.
* Each Product can have one or more ``Engagements``.
* Each Engagement can have one more ``Tests``.
* Each Test can have one or more ``Findings``.

.. image:: /_static/DD-Hierarchy.png

The code is open source, and `available on github`_.

A demo installation can be `found over at PythonAnywhere`_.

.. _available on github: https://github.com/rackerlabs/django-DefectDojo
.. _found over at PythonAnywhere: https://defectdojo.pythonanywhere.com



Our documentation is organized in the following sections:

* :ref:`user-docs`
* :ref:`feature-docs`
* :ref:`api-docs`

.. _user-docs:

User Documentation
------------------
.. toctree::
   :maxdepth: 2

   about
   getting-started
   integrations
   models
   start-using
   workflows
   upgrading
   running-in-production

.. _feature-docs:

Feature Documentation
---------------------

.. toctree::
   :maxdepth: 2
   :glob:

   features

.. _api-docs:

API Documentation
-----------------

.. toctree::
   :maxdepth: 2
   :glob:

   api-docs
   api-v2-docs
