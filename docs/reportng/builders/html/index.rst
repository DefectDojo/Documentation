HTML Report Builder
===================

The HTML report builder can insert data of Dojo into custom HTML templates.

You are **100% in control** of the appearance of your reports, because the builder
let's you provide templates which are then populated with the data from Dojo. While
in theory the templating engine can be configured and changed, the HTML Report Builder
comes with the `Django Template Language (DTL)`_ pre-enabled, which should be adequate
for most needs.

.. _`Django Template Language (DTL)`: https://docs.djangoproject.com/en/2.2/ref/templates/language/


.. _reportng/html/config:

Configuration
-------------

The builder class is ``dojo.reportng.builders.html.HTMLReportBuilder``.

These are all settings recognized by ``HTMLReportBuilder`` as JSON, ready to be added
to the ``DD_REPORTNG_BUILDERS`` list::

    {
        # The template backend could be changed, but this is the only compatible
        # implementation at the moment.
        "template_backend": "dojo.reportng.builders.base.SaferDjangoTemplates",

        # These have the same syntax as TEMPLATES[...]["options"] in Django settings
        # modules.
        # NOTE: Even when you only want to change a single of these settings from
        # its default value, you have to include the full template_backend_options
        # mapping in your config, because it can't be overwritten partially.
        "template_backend_options": {
            # Template tag libraries that should be loaded implicitly.
            "builtins": [
                # This one contains basic filters.
                "dojo.reportng.builders.html.templatetags.html"
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
                    "dojo.reportng.builders.html.FilesystemHTMLTemplateLoader",
                    # List of directories to scan for templates.
                    ["<MEDIA_ROOT>/reportng/html_templates"],
                    # Don't offer templates in subdirectories.
                    false
                ]
            ]
        }
    }


Report Templates
----------------

Everything described for :doc:`../tex/index` also holds for this builder.

Here's a minimalist sample template (``min.html``)::

.. literalinclude:: min.html


Template Tags and Filters
~~~~~~~~~~~~~~~~~~~~~~~~~

Django provides an extensive list of `built-in template tags and filters`_. The TeX
report builder extends this list with the following filters:

.. _`built-in template tags and filters`: https://docs.djangoproject.com/en/2.2/ref/templates/builtins/

* ``markdown_render``: 
  Converts a markdown string into HTML. Use this filter to embed fields containing
  markdown (like ``finding.description``) into the template.
