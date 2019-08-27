TeX Report Builder
==================

According to its name, you can use the TeX Report Builder to create and compile
respectable looking reports using LaTeX (or plain TeX), which can be compiled to
PDF format automatically.

You are **100% in control** of the appearance of your reports, because the builder
let's you provide templates which are then populated with the data from Dojo. While
in theory the templating engine can be configured and changed, the TeX Report Builder
comes with the `Django Template Language (DTL)`_ pre-enabled, which should be adequate
for most needs.

.. _`Django Template Language (DTL)`: https://docs.djangoproject.com/en/2.2/ref/templates/language/


.. _reportng/tex/config:

Configuration
-------------

The builder class is ``dojo.reportng.builders.tex.TeXReportBuilder``.

These are all settings recognized by ``TeXReportBuilder`` as JSON, ready to be added
to the ``DD_REPORTNG_BUILDERS`` list::

    {
        # The TeX report builder can output its reports in different formats:
        # - src: TeX source code only
        # - pdf: Compiled PDF only
        # - src_pdf: Both TeX source code and compiled PDF
        # The PDF formats are disabled by default because it's unsafe to blindly
        # compile TeX from untrusted sources.
        "formats": ["src"],

        # When the PDF formats have been enabled, you should set a command to be
        # executed to compile TeX to PDF. It is highly recommended to use some kind
        # of sandbox to isolate the compilation from the host. There are numerous
        # pre-built texlive Docker images which can be used. Maybe your command
        # should even compile the source twice to account for table of contents,
        # labels, references etc.
        # The command has to transform the source file "report.tex" into "report.pdf"
        # and log to "report.log". All files are located in the working directory.
        # The exit code must tell about the result (0 = success, other = failure).
        "pdf_cmd": "lualatex --interaction=batchmode --output-directory . --output-format pdf --safer --nosocket --no-shell-escape report.tex",

        # The template backend could be changed, but this is the only compatible
        # implementation at the moment.
        "template_backend": "dojo.reportng.builders.base.SaferDjangoTemplates",

        # These have the same syntax as TEMPLATES[...]["options"] in Django settings
        # modules.
        # NOTE: Even when you only want to change a single of these settings from
        # its default value, you have to include the full template_backend_options
        # mapping in your config, because it can't be overwritten partially.
        "template_backend_options": {
            # Django only supports autoescaping for HTML, hence we disable it.
            "autoescape": false,
            # Template tag libraries that should be loaded implicitly.
            "builtins": [
                # This one contains basic filters like "tex" for escaping strings.
                "dojo.reportng.builders.tex.templatetags.tex"
            ],
            # Context processors can populate the template context with additional values.
            "context_processors": [],
            # Add additional template tag libraries here, if desired. Only the
            # libraries listed here can be loaded in templates using {% load %}.
            "libraries": {},
            # Loaders are responsible for finding and loading templates.
            "loaders": [
                [
                    # We use this custom implementation with slight changes
                    # compared to the native FilesystemLoader of Django, which
                    # isn't supported directly.
                    "dojo.reportng.builders.tex.FilesystemTeXTemplateLoader",
                    # List of directories to scan for templates.
                    ["<MEDIA_ROOT>/reportng/tex_templates"],
                    # Don't offer templates in subdirectories.
                    false
                ]
            ]
        }
    }


Report Templates
----------------

Storage
~~~~~~~

.. note::

   All default values mentioned in this section can be changed via the
   ``template_backend_options`` setting (:ref:`see here <reportng/tex/config>`).

By default, templates are loaded from the filesystem, from a directory named
``reportng/tex_templates`` inside your media root. You should make this directory
writable for staff users only. Subdirectories are not scanned for templates by
default. A template file's name has to end in ``.tex``, and its content should be
the TeX source code of your report.


Template Rendering Context
~~~~~~~~~~~~~~~~~~~~~~~~~~

The objects you selected to be included in your report are available in the template
rendering context in a serialized format, where their data can be inserted using the
Django Template Language. The DTL is quite powerful and it's recommended to consult
the official documentation as a reference.

To keep things simple, the product, engagement, test and finding objects available
in the context have the same format as their respective endpoints in the REST API
v2. Just browse the interactive API docs to find out what properties are available.

The template rendering context is populated with the following data:

* ``title``: The report's title as entered in the builder UI.

* ``config``: If the user entered some custom YAML configuration for the selected
  template in the builder UI, this is the object the YAML has been deserialized to.

* ``products``: List of dictionaries representing the selected products. Additionally,
  each product has the following properties:

  * ``engagements``: List of dictionaries representing the selected engagements
    of that product. Additionally, each engagement has the following properties:

    * ``tests``: List of dictionaries representing the selected tests of that
      engagement. Additionally, each test has the following properties:

      * ``findings``: List of dictionaries representing the selected findings of
        that test. Additionally, each finding has the following properties:

        * ``images``: List of dictionaries representing the images of that finding.
          Additionally, each image has the following properties:

          * ``path``: Path to the image file, relative to the working directory
            when the TeX document is compiled. Images can then be included e.g. using
            ``\includegraphics`` from the ``graphicx`` package. You have to select
            an image size in the builder UI for image paths to be provided.


Inserting Data
~~~~~~~~~~~~~~

Here's a minimalist example (``min.tex``)::

.. literalinclude:: min.tex

.. warning::

   It's crucial to use the ``tex`` filter whenever data is inserted into the TeX
   source, as shown in the example. The filter escapes any character having a special
   meaning in TeX and prevents the injection of potentially malicious code.

After this snippet has been saved, you can navigate to the ReportNG Builder in
Dojo's user interface and create a TeX report using the newly written template
``min.tex``. Choose a title, "TeX source code only" as format and some products,
engagements, tests and findings. Then hit the "Build Report" button and download
your new report.

.. note::

   TeX reports are built asynchronously in the background. You may need to refresh
   the page until your report's state changes from "Building" to "Ready".


Template Tags and Filters
~~~~~~~~~~~~~~~~~~~~~~~~~

Django provides an extensive list of `built-in template tags and filters`_. The TeX
report builder extends this list with the following filters:

.. _`built-in template tags and filters`: https://docs.djangoproject.com/en/2.2/ref/templates/builtins/

* ``tex``:
  Escapes any character in a string that has a special meaning in TeX. **Always use
  this filter** when inserting data into the template.

* ``to_latex:"<format>"``:
  Converts a string from the specified format (default is ``"gfm"``) into
  LaTeX using pandoc. Use this filter to embed fields containing markdown (like
  ``finding.description``) into the template. The ``pandoc`` command line tool is
  required, which is already included in Dojo's official Docker image.


Custom Template Variables
~~~~~~~~~~~~~~~~~~~~~~~~~

As you might have noticed already, there is a large monospace textarea under "Report
Settings" in the builder UI. Here you can enter data in YAML syntax which is then
accessible in the template rendering context under the name ``config``.

Given the following YAML::

    lang: de
    toctree: false

you can use variables like so in your template::

    {% if config.toctree %}
        \tableofcontents
    {% endif %}

    {% if config.lang == "de" %}
        Dies ist ein wunderbarer Bericht!
    {% else %}
        This is a wonderful report!
    {% endif %}

However, the users of a template don't necessarily know the variables expected by that
particular template. That's why you can provide a per-template default config which
can be loaded into the builder UI. Just create a new file alongside your template
named ``<template_name>.tex.config`` with the default YAML content.

One of the beauties of YAML is that it supports comment lines (introduced by ``#``),
so you can easily describe what the different settings of your template do and which
values are accepted::

    # Language
    # Supported values: de, en
    lang: en

    # Whether to create a table of contents
    toctree: true

It's as easy as this. Even users not familiar to your templates can now switch the
language and decide whether to have a table of contents themselves, all of this without
requiring to change a single line of code in the template (or even in ReportNG).
