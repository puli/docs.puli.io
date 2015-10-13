Getting Started with the Repository Component
=============================================

The way you install and use the Repository Component depends on the type of
project you are working on:

* `In a Symfony Project`_, the Repository Component is installed through Puli's
  Symfony Bundle.
* `In a PHP Application`_ that will never be a dependency of other Composer
  packages, the Repository Component is installed through Puli's Composer Plugin.
* `In a Composer Package`_ that is a dependency of an application or other
  packages, the Repository Component needs to be installed manually.

In a Symfony Project
--------------------

.. important::

    Before you continue, :ref:`install the Puli CLI <cli-installation>` on your
    system.

In a Symfony application, the Repository Component is installed through
`Puli's Symfony Bundle`_. Before you install the bundle, set "minimum-stability"
to "beta" in ``composer.json``:

.. code-block:: json

    {
        "minimum-stability": "beta"
    }


The bundle can be installed with Composer_. `Install Composer`_ and enter the
following command in a terminal:

.. code-block:: text

    $ composer require puli/symfony-bundle:^1.0

As with every bundle, add the :class:`Puli\\SymfonyBundle\\PuliBundle` class to
your ``AppKernel``:

.. code-block:: php

    class AppKernel extends Kernel
    {
        public function registerBundles()
        {
            return array(
                // ...
                new Puli\SymfonyBundle\PuliBundle(),
            );
        }

        // ...
    }

Now that the bundle is installed, let's add our first |resource| to the Puli
repository. We will map the |path prefix| ``/app`` to the ``app/Resources``
directory of our application:

.. code-block:: text

    $ php puli.phar map /app app/Resources

All resources stored in the ``app/Resources`` directory can now be accessed
through the path prefix ``/app``. As example, we will add an ``index.html.twig``
file:

.. code-block:: text

    $ mkdir app/Resources/views
    $ echo "Success" > app/Resources/views/index.html.twig

Puli's :class:`Puli\\Repository\\Api\\ResourceRepository` can be used to access
all files found through these |path mappings|. Let's print the contents of the
``index.html.twig`` file in a controller:

.. code-block:: php

    class PostController
    {
        public function indexController()
        {
            $repo = $this->get('puli.repository');

            echo $repo->get('/app/views/index.html.twig')->getBody();
        }
    }

In a PHP Application
--------------------

.. important::

    Before you continue, :ref:`install the Puli CLI <cli-installation>` on your
    system.

In a PHP application, the Repository Component is installed through
`Puli's Composer Plugin`_. Before you install the plugin, set
"minimum-stability" to "beta" in ``composer.json``:

.. code-block:: json

    {
        "minimum-stability": "beta"
    }


The plugin can be installed with Composer_. `Install Composer`_ and enter the
following command in a terminal:

.. code-block:: text

    $ composer require puli/composer-plugin:^1.0

Now that the plugin is installed, let's add our first |resource| to the Puli
repository. We will map the |path prefix| ``/app`` to the ``res`` directory of
our application:

.. code-block:: text

    $ php puli.phar map /app res

All resources stored in the ``res`` directory can now be accessed through the
path prefix ``/app``. As example, we will add an ``index.html.twig`` file:

.. code-block:: text

    $ mkdir res/views
    $ echo "Success" > res/views/index.html.twig

Puli's :class:`Puli\\Repository\\Api\\ResourceRepository` can be used to access
all files found through these |path mappings|. Use the Puli factory to create
the :class:`Puli\\Repository\\Api\\ResourceRepository` instance:

.. code-block:: php

    $factoryClass = PULI_FACTORY_CLASS;
    $factory = new $factoryClass();

    $repo = $factory->createRepository();

.. include:: ../_di-note.rst

Let's print the contents of the ``index.html.twig`` file in our PHP code:

.. code-block:: php

    echo $repo->get('/app/views/index.html.twig')->getBody();

In a Composer Package
---------------------

.. important::

    Before you continue, :ref:`install the Puli CLI <cli-installation>` on your
    system.

In a Composer_ package, the Repository Component is installed manually. Before
you install the component, set "minimum-stability" to "beta" in
``composer.json``:

.. code-block:: json

    {
        "minimum-stability": "beta"
    }

`Install Composer`_ and enter the following command in a terminal:

.. code-block:: text

    $ composer require puli/repository:^1.0

Now that the component is installed, let's add our first |resource| to the Puli
repository. We will map the |path prefix| ``/batman/blog`` to the ``res``
directory of our package:

.. code-block:: text

    $ php puli.phar map /batman/blog res

.. note::

    By convention, path prefixes match the name of their Composer package. If
    your Composer package is called ``my/package``, use ``/my/package`` as Path
    Prefix.

All resources stored in the ``res`` directory can now be accessed through the
path prefix ``/batman/blog``. As example, we will add an ``index.html.twig``
file:

.. code-block:: text

    $ mkdir res/views
    $ echo "Success" > res/views/index.html.twig

Puli's :class:`Puli\\Repository\\Api\\ResourceRepository` can be used to access
all files found through these |path mappings|. Let's print the contents of the
``index.html.twig`` file in our PHP code:

.. code-block:: php

    use Puli\Repository\Api\ResourceRepository;

    class ResourcePrinter
    {
        private $repo;

        public function __construct(ResourceRepository $repo)
        {
            $this->repo = $repo;
        }

        public function printResources()
        {
            echo $this->repo->get('/app/views/index.html.twig')->getBody();
        }
    }

You should never create :class:`Puli\\Repository\\Api\\ResourceRepository`
instances in a Composer package. Instead, let the application that uses your
package create the repository and pass it to your code. This way, every part of
the application uses the same instance and benefits of caching and other
optimizations done internally.

Further Reading
---------------

* Read :doc:`directory-layout` to learn about the recommended directory layout
  for Puli projects.
* Read :doc:`mapping-resources` to learn about creating and modifying path
  mappings with the Puli CLI.
* Read :doc:`working-with-resources` to learn how to use the
  :class:`Puli\\Repository\\Api\\ResourceRepository` API to access and work with
  resources in your PHP code.
* Read :doc:`stream-wrappers` to learn how to register a PHP stream wrapper for a
  Puli repository.

.. _Puli's Symfony Bundle: https://github.com/puli/symfony-bundle
.. _Puli's Composer Plugin: https://github.com/puli/composer-plugin
.. _Composer: https://getcomposer.org
.. _Install Composer: https://getcomposer.org/doc/00-intro.md

.. |resource| replace:: :ref:`resource <glossary-resource>`
.. |path prefix| replace:: :ref:`path prefix <glossary-path-prefix>`
.. |path mappings| replace:: :ref:`path mappings <glossary-path-mapping>`
