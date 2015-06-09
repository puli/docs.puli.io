.. index::
    single: Getting Started
    single: Installation


Features
========

Discover Resources in Other Packages
------------------------------------

With Puli, your installed Composer packages recognize each other automatically.
For example, if you install the package "thor/translator", the translator is
able to load all translation files (like ``messages.en.xlf``) in any other
installed package without extra configuration. This process is called *resource
discovery*. *Resources*, in Puli's language, are files that are not PHP, such as
YAML, XML, CSS, JavaScript etc.

.. image:: images/discovery.png
   :alt: Puli's resource discovery mechanism.
   :align: center

Resource discovery is simple: *Resource consumers*, like our translator, give a
name to the resources they want to load, such as "thor/translations". This name
is called a *binding type*. Other packages (the *resource providers*) assign
their translation files to this type. This is called *binding*. In the end Puli
is able to hand the bound resources over to the translator.

:doc:`Learn more about the Discovery Component » <../discovery/introduction>`

Load Resources Anywhere
-----------------------

Puli provides a framework-agnostic naming convention to access resources in
Composer packages. Each resource has a *Puli Path* that starts with the name of
the Composer package that contains that resource. Use this path to access
resources in your code:

.. code-block:: php

    // vendor/acme/blog/res/views/index.html.twig
    echo $twig->render('/acme/blog/views/index.html.twig');

Puli resolves this path by reading all ``puli.json`` files found in your
installed Composer packages. These files contain mappings from path prefixes to
actual file paths. In this example, the developer of the "acme/blog" package
added a mapping from the ``/acme/blog`` prefix to the ``res`` directory in that
package with Puli's Command Line Interface:

.. code-block:: text

    $ php puli.phar map /acme/blog res

:doc:`Learn more about the Repository Component » <../repository/introduction>`

.. _feature-resource-overriding:

Override Resources in Other Packages
------------------------------------

With Puli, you always load resources through their Puli Paths. This abstraction
allows you to override resources in other Composer packages simply by mapping
the Puli Path to your customized version of the resource.

.. code-block:: text

    $ php puli.phar map /acme/blog/views/layout.html.twig res/views/layout.html.twig

After adding the mapping, your custom ``layout.html.twig`` will be used whenever
the file from the "acme/blog" package is loaded.

:doc:`Learn more about the Repository Component » <../repository/introduction>`

.. _feature-public-resources:

Publish Resources and Generate their URLs
-----------------------------------------

While some resources - like configuration files - are needed internally by your
application only, others - like CSS or images - need to be accessible by your
visitors. Puli calls them *public resources*.

With Puli, you have full control over where the public resources of your
Composer packages are installed and how their URLs are generated. For example,
you could serve all images from a Content Delivery Network (CDN) using a few
lines of configuration.

:doc:`Learn more about the URL Generator Component » <../url-generator/introduction>`
