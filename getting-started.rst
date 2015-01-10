.. index::
    single: Getting Started
    single: Installation

.. |trade| unicode:: U+2122

Getting Started
===============

This section explains how to get started quickly with Puli_. If you don't know
what Puli is or why you should use it, read :doc:`at-a-glance` first.

Requirements
------------

Puli requires PHP 5.3.9 or higher.

Stability
---------

Puli is not yet available in a stable version. The following table lists the
current version of Puli's core components as well as their expected stable
release dates:

=================  =====================================  ============= ===================
Component          Description                            Version       Stable Release
=================  =====================================  ============= ===================
Repository_        The PHP resource repository API        1.0.0-alpha4  Jan 2015
Discovery_         The PHP resource discovery API         1.0.0-dev     Jan 2015
Factory_           The factory API for Puli's services    1.0.0-dev     Jan 2015
CLI_               The Command-Line Interface             1.0.0-alpha1  Feb-Mar 2015
=================  =====================================  ============= ===================

Installation
------------

To install Puli, add Puli's components to your composer.json file:

.. code-block:: json

    {
        "require": {
            "puli/repository": "~1.0",
            "puli/discovery": "~1.0",
            "puli/factory": "~1.0"
        },
        "require-dev": {
            "puli/cli": "~1.0"
        },
        "minimum-stability": "dev"
    }

.. tip::

    You can omit the Discovery_ component if you don't need it.

Run ``composer update`` to install the packages. Type ``vendor/bin/puli`` to
check whether the Puli CLI was installed successfully:

.. code-block:: text

    $ vendor/bin/puli

.. caution::

    The leading ``$`` in the code examples is a convention that tells you that
    the example contains shell code. Type the command that follows the dollar
    sign in your terminal, but don't type the ``$`` itself.

.. note::

    If "vendor-dir" or "bin-dir" is set in composer.json, the path to the
    ``puli`` command will be different.

To save you from typing ``vendor/bin/puli`` all the time, add the ``vendor/bin``
directory to your path:

* On Unix-based systems, add the following line to ~/.bashrc:

    .. code-block:: bash

        export PATH="$PATH:vendor/bin"

  Apply the changes with the ``source`` command:

    .. code-block:: text

        $ source ~/.bashrc

* On Windows, append ``;vendor/bin`` to the environment variable "Path". In
  Windows 7, you can find the environment variables in the Control Panel
  under "System" → "Advanced system settings" → "Advanced" →
  "Environment Variables".

.. caution::

    If "vendor-dir" or "bin-dir" is set in composer.json, adapt the content
    of the path accordingly.

Now you should be able to run ``puli`` without the ``vendor/bin/`` prefix.

Loading the Puli Factory
------------------------

The Puli CLI generates a factory that creates Puli's core services for you. The
class of the generated factory is stored in the constant PULI_FACTORY_CLASS:

.. code-block:: php

    $factoryClass = PULI_FACTORY_CLASS;
    $factory = new $factoryClass();

Use this factory to create Puli's :class:`Puli\\Repository\\Api\\ResourceRepository`
and :class:`Puli\\Discovery\\Api\\ResourceDiscovery` instances:

.. code-block:: php

    $repo = $factory->createRepository();
    $discovery = $factory->createDiscovery($repo);

These instances give you access to all resources mapped via the Puli CLI. For
performance reasons, they should be created only once in your project and passed
to all classes that require them.

Directory Layout Recommendation
-------------------------------

We recommend to follow a certain directory layout in your project. This is by
no means mandatory, but it will improve your experience when working with Puli.

Most importantly, we recommend to separate PHP code and non-PHP resources into
two separate top-level directories:

.. code-block:: text

    src/
        MyService.php
        ...
    res/
        config/
            config.yml
        ...

The names of these directories don't matter -- you can name them ``source``,
``resources`` or whatever else you prefer. The important point is that the two
directories do not overlap. If the directories overlap, both the class
autoloader and the resource repository need to process unnecessary files.

Second, we recommend to use the following names for the sub-directories of the
resource directory:

.. code-block:: text

    config/
        ... configuration files ...
    public/
        css/
            ... CSS files ...
        js/
            ... Javascript files ...
        images/
            ... images ...
    trans/
        ... translation files ...
    views/
        ... templates ...

Using common names ensures a consistent user experience when referencing
resources in your project and any other Puli-enabled package:

.. code-block:: php

    // Rendering an application template with Twig
    $twig->render('/app/views/index.html');

    // Rendering a package template with Twig
    $twig->render('/acme/blog/views/post/show.html.twig');

The public resources are bundled in a directory ``public`` because this way
these resources can be easily copied to sub-directories of your public
directory:

.. code-block:: text

    /app/public/* -> /public_html/
    /acme/blog/public/* -> /public_html/blog/
    ...

Further Reading
---------------

* Read :doc:`mapping-resources` to learn how to map Puli paths to files and
  directories.
* Read :doc:`working-with-resources` to learn how to use the resources returned
  by the generated repository.

.. _Puli: https://github.com/puli/puli
.. _Puli CLI: https://github.com/puli/cli
.. _Composer Plugin: https://github.com/puli/composer-plugin
.. _Composer: https://getcomposer.org
.. _Repository: https://github.com/puli/repository
.. _Discovery: https://github.com/puli/discovery
.. _Factory: https://github.com/puli/factory
.. _CLI: https://github.com/puli/cli
