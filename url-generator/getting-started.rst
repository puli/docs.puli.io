Getting Started with the URL Generator Component
================================================

The way you install and use the URL Generator Component depends on the type of
project you are working on:

* `In a Symfony Project`_, the URL Generator Component is installed through Puli's
  Symfony Bundle.
* `In a PHP Application`_ that will never be a dependency of other Composer
  packages, the URL Generator Component is installed through Puli's Composer
  Plugin.
* `In a Composer Package`_ that is a dependency of an application or other
  packages, the URL Generator Component needs to be installed manually.

In a Symfony Project
--------------------

.. important::

    Before you continue, :ref:`install the Puli CLI <cli-installation>` and
    :doc:`the Repository Component <../repository/getting-started>` in your
    project.

In a Symfony application, the URL Generator Component is installed through
`Puli's Symfony Bundle`_. If you followed :doc:`../repository/getting-started`,
this bundle is already installed in your application.

With Puli's :class:`Puli\\UrlGenerator\\Api\\UrlGenerator` class you can
generate URLs for a |Puli path|. Let's print the URL of the
``app/Resources/public/images/logo.png`` file in a controller:

.. code-block:: php

    class PostController
    {
        public function indexController()
        {
            $generator = $this->get('puli.url_generator');

            echo $generator->generateUrl('/app/public/images/logo.png');
        }
    }

.. tip::

    In Twig templates you can use the ``resource_url()`` function of
    :doc:`Puli's Twig Extension <../extensions/twig>`. See the
    :doc:`documentation of the extension <../extensions/twig>` for more
    information.

Before the above code actually works, you need to tell Puli how to generate your
URLs. Add a |web server| for your ``web`` directory with the Puli CLI:

.. code-block:: text

    $ php puli.phar server --add localhost web

In the example, the server is named "localhost", but you can choose any name you
like.

Next, publish the resources under ``/app/public`` to the server:

.. code-block:: text

    $ php puli.phar publish /app/public localhost

Now the URL is generated correctly in the template:

.. code-block:: html+jinja

    <img src="/images/logo.png" />

You can install all public resources in the ``web`` directory with the
``publish --install`` command:

.. code-block:: text

    $ php puli.phar publish --install
    Installing /app/public into web via symlink...

In a PHP Application
--------------------

.. important::

    Before you continue, :ref:`install the Puli CLI <cli-installation>` and
    :doc:`the Repository Component <../repository/getting-started>` in your
    project.

In a PHP application, the URL Generator Component is installed through
`Puli's Composer Plugin`_. If you followed :doc:`../repository/getting-started`,
this bundle is already installed in your application.

With Puli's :class:`Puli\\UrlGenerator\\Api\\UrlGenerator` class you can
generate URLs for a |Puli path|. Use the Puli factory to create the
:class:`Puli\\UrlGenerator\\Api\\UrlGenerator` instance:

.. code-block:: php

    $factoryClass = PULI_FACTORY_CLASS;
    $factory = new $factoryClass();

    $repo = $factory->createRepository();
    $discovery = $factory->createDiscovery($repo);
    $generator = $factory->createUrlGenerator($discovery);

.. include:: ../_di-note.rst

Use the :method:`Puli\\UrlGenerator\\Api\\UrlGenerator::generateUrl` method to
generate URLs for a |resource|:

.. code-block:: php

    echo $generator->generateUrl('/app/public/images/logo.png');

.. tip::

    Install :doc:`Puli's Twig Extension <../extensions/twig>` to generate URLs
    in Twig templates.

This code will echo the URL for the ``res/public/images/logo.png`` file. Before
the code actually works, you need to tell Puli how to generate your URLs. Add a
|web server| for the document root of your web server with the Puli CLI:

.. code-block:: text

    $ php puli.phar server --add localhost public_html

In the example, the server is named "localhost", but you can choose any name you
like. We assume that the document root of the server is the ``public_html``
directory in your project.

Next, publish the resources under ``/app/public`` to the server:

.. code-block:: text

    $ php puli.phar publish /app/public localhost

Now the URL is generated correctly:

.. code-block:: php

    echo $generator->generateUrl('/app/public/images/logo.png');
    // => /images/logo.png

You can install all public resources in the ``public_html`` directory with the
``publish --install`` command:

.. code-block:: text

    $ php puli.phar publish --install
    Installing /app/public into public_html via symlink...

In a Composer Package
---------------------

.. important::

    Before you continue, :ref:`install the Puli CLI <cli-installation>` and
    :doc:`the Repository Component <../repository/getting-started>` in your
    project.

In a Composer package, the URL Generator Component is installed manually. Before
you install the component, set "minimum-stability" to "beta" in
``composer.json``:

.. code-block:: json

    {
        "minimum-stability": "beta"
    }

Install the component with Composer:

.. code-block:: text

    $ composer require puli/url-generator:^1.0


With Puli's :class:`Puli\\UrlGenerator\\Api\\UrlGenerator` class you can
generate URLs for a |Puli path|. Use the
:method:`Puli\\UrlGenerator\\Api\\UrlGenerator::generateUrl` method to
generate URLs for a |resource|:

.. code-block:: php

    echo $generator->generateUrl('/app/public/images/logo.png');

.. tip::

    Install :doc:`Puli's Twig Extension <../extensions/twig>` to generate URLs
    in Twig templates.

This code will echo the URL for the ``res/public/images/logo.png`` file.
However, before this code actually works, the application that uses your package
must register a |web server| and publish your resources there.

You should never create :class:`Puli\\UrlGenerator\\Api\\UrlGenerator`
instances in a Composer package. Instead, let the application that uses your
package create the URL generator and pass it to your code. This way, every part
of the application uses the same instance and benefits of caching and other
optimizations done internally.

Further Reading
---------------

* Read :doc:`server-configuration` to learn more about configuring web servers.

.. _Puli's Symfony Bundle: https://github.com/puli/symfony-bundle
.. _Puli's Composer Plugin: https://github.com/puli/composer-plugin

.. |web server| replace:: :ref:`web server <glossary-web-server>`
.. |resource| replace:: :ref:`resource <glossary-resource>`
.. |Puli path| replace:: :ref:`Puli path <glossary-puli-path>`
