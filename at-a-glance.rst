Puli at a Glance
================

Puli_ (pronounced "poo-lee") is a PHP library based on Composer_ that manages
the resources of your project and its dependencies. *Resources* can be anything,
but usually we use the term for machine-processed files that are *not* PHP, such
as CSS, JavaScript, XLIFF, YAML, XML or HTML files.

Puli is efficient, platform-agnostic and independent of any single framework.

Resource Loading
----------------

Just like Composer builds a PSR-4_ autoloader that knows how to load your
classes, Puli builds a *resource repository* from the files in your packages.
You can load these files using normalized paths. These *Puli paths* start with
the name of the package:

.. code-block:: php

    echo $twig->render('/acme/blog/views/footer.html.twig');

Every package developer is responsible for mapping their resources with the
"map" command of `Puli's Command Line Interface`_ (CLI):

.. code-block:: text

    # Map the prefix "/acme/blog" to the "res" directory
    $ puli map /acme/blog res

The configuration is stored in a ``puli.json`` file that is distributed with the
package - just like ``composer.json``. Puli reads the ``puli.json`` files of all
installed packages and makes their resources available to your project.

Read :doc:`mapping-resources` if you want to learn more.

Resource Overriding
-------------------

When working with third-party packages, it is often necessary to customize
files provided by these packages. With Puli this becomes a no-brainer: You
simply copy the files to your project and override the path mappings:

.. code-block:: text

    $ puli map /acme/blog/views/footer.html res/views/footer.html


The file ``res/views/footer.html`` stored in your project will now be used
whenever the resource ``/acme/blog/views/footer.html`` is requested.

Read :doc:`mapping-resources` if you want to learn more.

Web Resources
-------------

Some resources - such as templates or configuration files - are needed by the
web server only. Others - like CSS files and images - need to be placed in
a public directory, where browsers can download them.

`Puli's Web Resource Plugin`_ provides a generic solution for this problem.
With that plugin, you can map resources to paths in so-called *install targets*
(local directories, web servers, CDNs, ...):

.. code-block:: text

    # Create target "local" pointing to the "public_html" directory
    $ puli target add local public_html

    # Map resources to the "public_html/blog" directory
    $ puli web add /acme/blog/public /blog

Puli's "web install" command takes care of copying the resources to their
targets:

.. code-block:: text

    $ puli web install
    Installing /acme/blog/public into public_html/blog via symlink...

With Puli's URL generator, you can finally generate the URLs for the published
resources. The following example uses `Puli's Twig Extension`_:

.. code-block:: jinja

    <img src="{{ resource_url('/acme/blog/images/header.png') }}" />

Read :doc:`web-resources` if you want to learn more.

Stream Wrappers
---------------

Did you know PHP's `stream wrapper`_ feature? With Puli's stream wrapper, you
can use Puli resources like ordinary files:

.. code-block:: php

    // vendor/acme/blog/res/trans/en.yml
    echo file_get_contents('puli:///acme/blog/trans/en.yml');

Read :doc:`stream-wrapper` if you want to learn more.

Resource Discovery
------------------

Puli is able to associate resources with custom *binding types*. For example,
the `Doctrine ORM`_ package could define the binding type
``doctrine/xml-mapping``:

.. code-block:: text

    $ puli type define doctrine/xml-mapping

Any package that ships Doctrine entities maps its XML mapping files to that
binding type:

.. code-block:: text

    $ puli bind /acme/blog/config/doctrine/*.xml doctrine/xml-mapping

With Puli's :class:`Puli\\Discovery\\Api\\ResourceDiscovery`, Doctrine is able
to load these files:

.. code-block:: php

    foreach ($discovery->findByType('doctrine/xml-mapping') as $binding) {
        foreach ($binding->getResources() as $resource) {
            // process $resource...
        }
    }

Thanks to Puli's resource discovery, the files and classes in your installed
packages are wired together automatically.

Read :doc:`discovery` if you want to learn more.

Further Reading
---------------

* Read :doc:`getting-started` to learn how to install Puli in your project.

.. _Puli: https://github.com/puli/puli
.. _Composer: https://getcomposer.org
.. _PSR-4: http://www.php-fig.org/psr/psr-4/
.. _Puli's Command Line Interface: https://github.com/puli/cli
.. _`Puli's Web Resource Plugin`: https://github.com/puli/web-resource-plugin
.. _Composer plugin: https://github.com/puli/composer-plugin
.. _Puli's Twig Extension: https://github.com/puli/twig-extension
.. _stream wrapper: http://php.net/manual/en/intro.stream.php
.. _Doctrine ORM: http://www.doctrine-project.org/projects/orm.html
