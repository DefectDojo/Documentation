ReportNG
========

ReportNG stands for *Next Generation Report* and is a new Dojo component. It
shouldn't be mixed with the legacy report builder, which is still there as an
alternative. Both can work side by side.

ReportNG is a flexible framework for implementing report builders with two
implementations for the time being.

It let's you create reports of only those products, engagements, tests and findings
you want to be included. The chosen report builder implementation then cares about
the building process, output format and appearance. That way, data selection and
formatting of the report are strictly decoupled.

.. toctree::
   :caption: Builder Implementations
   :maxdepth: 1

   builders/html/index
   builders/tex/index

.. note::

   If you're a developer and want to contribute a new report builder implementation,
   just take the existing TeX builder as an example. It's really not hard to create
   your own.


.. _reportng/config:

Configuration
-------------

The builder implementations to activate are configured using the
``DD_REPORTNG_BUILDERS`` setting. The value of this setting is a list (in JSON),
where each list item can either be just a string (the path of the builder class)
or a mapping with multiple general and/or implementation-specific settings.

.. warning::

   JSON has no support for comments, hence these samples are, strictly speaking,
   no valid JSON. If you copy parts of the mapping, remove all comment lines (those
   starting with ``#``) and ensure there are no trailing commas in lists and mappings
   to get valid JSON.

These settings are available for all builder implementations::

    {
        "class": "path.to.the.builder.Class",

        # An internal representation of this builder instance (<= 20 characters).
        # Each builder implementation has a default code, but should you configure
        # more than one instance of the same builder implementation, you need to
        # set them yourself.
        "code": "...",

        # The visible name of this builder instance. Each implementation has its
        # default, but should you configure multiple instances of the same builder
        # implementation, you should set them yourself to avoid confusion.
        name: "..."
    }

In addition to these, each builder implementation has its own settings. See the
respective documentation for a complete list.

For configuring a single builder implementation (the ``TeXReportBuilder`` in this
case), the ``DD_REPORTNG_BUILDERS`` environment variable could look like::

    DD_REPORTNG_BUILDERS='[{"class":"dojo.reportng.builders.tex.TeXReportBuilder", "formats":["src", "src_pdf"]}]'


The Builder Interface
---------------------

One of the key goals when creating the report builder's web interface was ensuring
simplicity and good usability. The result is a single page, divided into five
consistently looking panels representing the steps of the report creation process:

1. General Report Settings
2. Products Selection
3. Engagements Selection
4. Tests Selection
5. Findings Selection

The first step covers basic settings, such as the report's title. This is also the
place where builder implementation-specific settings and choices can be made. Consult
the respective documentation for more information.

Steps 2-5 build a hirarchy, meaning the objects you select in one step restrict the
choices available in the following one. Objects need to be ticked in order to count
as selected, but you can of course just use the "Select/Deselect All".buttons.

All changes you made to one of the panels are committed and represented in the
UI after you press ``RETURN``, click one of the "Apply" buttons, navigate through
paginated object lists or switch the expanded panel. Even selections of objects on
other pages are retained.


Drafts
------

A report can be stored as draft by clicking "Save draft" at the bottom of the builder
page. Drafts can be seen as snapshots of that builder page, including all settings,
filters and objects you selected. When you're satisfied with the state of a draft,
it can either be converted into a real report directly or copied before and kept
for future use.

.. note::

   Even a report which has been built already can be used as template for a new one
   by choosing "Open in report builder" from its options menu.


Permissions
-----------

Permissions are enforced everywhere throughout ReportNG.

* Staff members can read and write every report.
* Users are only presented the products, engagements, tests and findings they have
  access to when creating a report.
* Drafts are always private to the user who created them.
* Users can only view a report when they have access to all objects contained in
  the report.
* Only the respective requester can delete a report.
