Puli at a Glance
================

Puli_ (pronounced "poo-lee") is a PHP library that manages files, directories
and other resources in a repository - much like a virtual filesystem.
*Resources* can be anything, but usually we use the term for machine-processed
files that are *not* PHP, such as CSS, JavaScript, XLIFF, YAML, XML or HTML
files.

Why Puli?
---------

Many people use Composer_ to distribute their packages. These packages contain
both PHP files and resources that are used by the application. Loading PHP files
is easy, thanks to PSR-4_ and Composer's generated autoloader. Loading resources,
however, requires using absolute file paths with plenty of ``..`` segments or
inventing some sort of naming convention, which varies from project to project:

.. code-block:: jinja

    {% include 'AcmeBlogBundle::footer.html.twig' %}

Puli offers a platform- and framework-agnostic solution to this problem.

Repositories
------------

At its core, Puli provides a simple repository, much like a filesystem. You can
map paths in the repository to files or directories in your project with
`Puli's Command Line Interface`_ (CLI):

.. code-block:: text

    $ puli map /app res

Here, the *Puli path* ``/app`` is mapped to the directory ``res`` in our project.
This allows to access any file in the ``res`` directory by a Puli path:

.. code-block:: php

    // res/views/index.html
    echo $repo->get('/app/views/index.html')->getBody();

.. note::

    The variable ``$repo`` contains a
    :class:`Puli\\Repository\\Api\\ResourceRepository` instance. You will learn
    in :doc:`getting-started` how to load this object.

Composer Integration
--------------------

When you type ``puli map``, the mappings are saved in a puli.json file in the
root of your project. This file needs to be committed to your Version Control
System (for example Git) and distributed with the project.

If your project depends on other libraries through Composer, you can access the
resources mapped by these libraries thanks to Puli's `Composer plugin`_.
For example, assume that the author of the "acme/blog" package distributes a
puli.json file with the following mapping:

.. code-block:: text

    $ puli map /acme/blog res

When you install the "acme/blog" package, you can access any resources of the
package's ``res`` directory through the Puli path ``/acme/blog``:

.. code-block:: php

    // vendor/acme/blog/res/views/index.html
    echo $repo->get('/acme/blog/views/index.html')->getBody();

Tool Integration
----------------

Puli provides integration layers for several PHP libraries. The
`Twig extension`_, for example, can be used to load Twig templates via Puli
paths:

.. code-block:: jinja

    {% include '/acme/blog/views/footer.html.twig' %}

The `Symfony bridge`_ permits the use of Puli paths in configuration files:

.. code-block:: yaml

    # routing.yml
    _acme_blog:
        resource: /acme/blog/config/routing.yml

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
.. _Composer plugin: https://github.com/puli/composer-plugin
.. _Twig extension: https://github.com/puli/twig-extension
.. _Symfony bridge: https://github.com/puli/symfony-bridge
.. _stream wrapper: http://php.net/manual/en/intro.stream.php
.. _Doctrine ORM: http://www.doctrine-project.org/projects/orm.html
