The Puli Extension for Twig
===========================

Puli provides an extension_ for the `Twig templating engine`_. With this
extension, you can refer to template files through |Puli paths|:

.. code-block:: php

    echo $twig->render('/acme/blog/views/show.html.twig');

The extension also adds a ``resource_url()`` function for generating URLs for
|public resources|:

.. code-block:: html+jinja

    <img src="{{ resource_url('/app/public/images/logo.png') }}" />

Installation
------------

.. important::

    Before you continue, :ref:`install the Puli CLI <cli-installation>` and
    :doc:`the Repository Component <../repository/getting-started>` in your
    project.

Install the extension with Composer_:

.. code-block:: text

    $ composer require puli/twig-extension:^1.0

Configuration
-------------

To activate the extension, create a new
:class:`Puli\\TwigExtension\\PuliTemplateLoader` and register it with Twig.
The loader enables Twig to load templates through Puli paths:

.. code-block:: php

    use Puli\TwigExtension\PuliTemplateLoader;

    $twig = new \Twig_Environment(new PuliTemplateLoader($repo));

The loader receives Puli's :class:`Puli\\Repository\\Api\\ResourceRepository` as
only argument.

Next, create a new :class:`Puli\\TwigExtension\\PuliExtension` and add it to
Twig. The extension adds the ``resource_url()`` function and does a few more
tweaks to properly support Puli in Twig:

.. code-block:: php

    use Puli\TwigExtension\PuliExtension;

    $twig->addExtension(new PuliExtension($repo));

Usage in Twig
-------------

Using Puli in Twig is straight-forward: Use Puli paths wherever you would
usually use a file path. For example:

.. code-block:: jinja

    {% extends '/acme/blog/views/layout.html.twig' %}

    {% block content %}
        {# ... #}
    {% endblock %}

Contrary to Twig's default behavior, you can also refer to templates using
relative paths:

.. code-block:: jinja

    {% extends 'layout.html.twig' %}

    {% block content %}
        {# ... #}
    {% endblock %}

Resource URLs
-------------

You can generate URLs for public Puli resources with the ``resource_url()``
function:

.. code-block:: html+jinja

    <img src="{{ resource_url('/app/public/images/logo.png') }}" />

The function accepts both absolute and relative paths:

.. code-block:: html+jinja

    <img src="{{ resource_url('../public/images/logo.png') }}" />

.. note::

    The resource must have been published with the ``publish`` command of the
    Puli CLI, otherwise the URL generator will fail. See the
    :doc:`URL generator documentation <../url-generator/introduction>` for more
    information.

.. _Composer: https://getcomposer.org
.. _Twig templating engine: http://twig.sensiolabs.org
.. _extension: https://github.com/puli/twig-extension

.. |Puli paths| replace:: :ref:`Puli paths <glossary-puli-path>`
.. |public resources| replace:: :ref:`public resources <glossary-public-resource>`
