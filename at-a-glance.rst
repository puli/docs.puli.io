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

The configuration is stored in a `puli.json` file that is distributed with the
package - just like `composer.json`. Puli reads the `puli.json` files of all
installed packages and makes their resources available to your project.

Read :doc:`mapping-resources` if you want to learn more.

Web Resources
-------------

Some resources - such as templates or configuration files - are needed by the
web server only. Others - like CSS and JavaScript files - need to be placed in
a public directory, so that browsers can download them.

`Puli's Web Resource Plugin`_ provides a generic solution for this problem.
With that plugin, you can map resources to paths in so-called *install targets*
(local directories, web servers, CDNs, ...):

.. code-block:: text

    # Publish resources to the "public_html" directory
    $ puli target add local public_html

    # Map the "acme/blog" resources to the "public_html/blog" directory
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

Resource Overriding
-------------------

Consider that you want to change the contents of
``/acme/blog/views/footer.html.twig`` in the "acme/blog" package without
touching that package. With Puli, you can copy the file to your project and
override it in your project:

.. code-block:: text

    $ puli map /acme/blog/views/footer.html res/views/footer.html


The file ``res/views/footer.html`` stored in your project will now be returned
whenever the resource ``/acme/blog/views/footer.html`` is used.

Stream Wrappers
---------------

Did you know PHP's `stream wrapper`_ feature? With Puli's stream wrapper, you
can use Puli resources like ordinary files:

.. code-block:: php

    use Puli\Repository\StreamWrapper\ResourceStreamWrapper;

    ResourceStreamWrapper::register('puli', $repo);

In this example, the resource repository is registered for the "puli://" scheme
through Puli's :class:`Puli\\Repository\\StreamWrapper\\ResourceStreamWrapper`.
Now you can access Puli resources like normal files, as long as you prefix them
with "puli://":

.. code-block:: php

    // vendor/acme/blog/res/trans/en.yml
    echo file_get_contents('puli:///acme/blog/trans/en.yml');

Resource Discovery
------------------

Many tools require you to provide configuration, translations or other content
in files of specific formats. For example, the `Doctrine ORM`_ is able to load
the configuration of a ``MyProject\User`` entity from a
``MyProject.User.dcm.xml`` file:

.. code-block:: xml

    <!-- res/config/doctrine/MyProject.User.dcm.xml -->
    <doctrine-mapping ...>
        <entity name="MyProject\User">
            <field name="name" type="string" />
        </entity>
    </doctrine-mapping>

Registering all such files with the tool that uses them requires some effort,
especially once they are spread across several Composer packages.

Puli supports a very simple resource discovery mechanism to solve this problem.
Libraries define *binding types* for the resources they want to process:

.. code-block:: text

    $ puli type define doctrine/xml-mapping

Your project and any other Composer package can now *bind* resources to these
types:

.. code-block:: text

    $ puli bind /app/config/doctrine/*.xml doctrine/xml-mapping

The library finally uses Puli's :class:`Puli\\Discovery\\Api\\ResourceDiscovery`
to access all the resources bound to its type:

.. code-block:: php

    foreach ($discovery->find('doctrine/xml-mapping') as $binding) {
        foreach ($binding->getResources() as $resource) {
            // process $resource...
        }
    }

When you install a Composer package, its bindings are not enabled by default:

.. code-block:: text

    $ puli bind
    Bindings that are neither enabled nor disabled:
     (use "puli bind --enable <uuid>" to enable)

        acme/blog
        fc20d8 /acme/blog/config/doctrine/*.xml doctrine/xml-mapping

This way you can selectively control which bindings you want to use in your
project.

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
