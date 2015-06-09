Getting Started with the Discovery Component
============================================

The way you install and use the Discovery Component depends on the type of
project you are working on:

* `In a Symfony Project`_, the Discovery Component is installed through Puli's
  Symfony Bundle.
* `In a PHP Application`_ that will never be a dependency of other Composer
  packages, the Discovery Component is installed through Puli's Composer Plugin.
* `In a Composer Package`_ that is a dependency of an application or other
  packages, the Discovery Component needs to be installed manually.

In a Symfony Project
--------------------

.. important::

    Before you continue, :ref:`install the Puli CLI <cli-installation>` and
    :doc:`the Repository Component <../repository/getting-started>` in your
    project.

In a Symfony application, the Discovery Component is installed through
`Puli's Symfony Bundle`_. If you followed :doc:`../repository/getting-started`,
this bundle is already installed in your application.

As a |resource consumer|, you can define a new |binding type| with the
``type --define`` command of the Puli CLI:

.. code-block:: text

    $ php puli.phar type --define thor/translations

.. note::

    The name of a binding type must always start with the vendor name of your
    Composer package. This prevents naming collisions and makes sure that you
    *own* the type.

The ``type --define`` command adds the new binding type to your ``puli.json``.

Use Puli's :class:`Puli\\Discovery\\Api\\ResourceDiscovery` to access all
|bindings| bound to your binding type. Each
:class:`Puli\\Discovery\\Api\\ResourceBinding` instance may refer to more
than one :class:`Puli\\Repository\\Api\\Resource`, for example if the bound path
contains the wildcard "*". Consequently you need a double loop to access the
resources in your code:

.. code-block:: php

    class PostController
    {
        public function indexController()
        {
            $bindings = $this->get('puli.discovery')->findByType('thor/translations');

            foreach ($bindings as $binding) {
                foreach ($binding->getResources() as $resource) {
                    echo $resource->getPath();
                }
            }
        }
    }

As a |resource provider|, you can bind resources to a binding type with the
``bind`` command of the Puli CLI:

.. code-block:: text

    $ php puli.phar bind /app/trans/*.yml thor/translations

The ``bind`` command adds the new binding to your ``puli.json``.

If a resource consumer and a resource provider are installed at the same
time, Puli makes sure that the consumer receives all resources bound to its
binding type by the provider.

In a PHP Application
--------------------

.. important::

    Before you continue, :ref:`install the Puli CLI <cli-installation>` and
    :doc:`the Repository Component <../repository/getting-started>` in your
    project.

In a PHP application, the Discovery Component is installed through
`Puli's Composer Plugin`_. If you followed :doc:`../repository/getting-started`,
this bundle is already installed in your application.

As a |resource consumer|, you can define a new |binding type| with the
``type --define`` command of the Puli CLI:

.. code-block:: text

    $ php puli.phar type --define thor/translations

.. note::

    The name of a binding type must always start with the vendor name of your
    Composer package. This prevents naming collisions and makes sure that you
    *own* the type.

The ``type --define`` command adds the new binding type to your ``puli.json``.

With Puli's :class:`Puli\\Discovery\\Api\\ResourceDiscovery` class you can
access all |bindings| bound to your binding type. Use the Puli factory to create
the :class:`Puli\\Discovery\\Api\\ResourceDiscovery` instance:

.. code-block:: php

    $factoryClass = PULI_FACTORY_CLASS;
    $factory = new $factoryClass();

    $repo = $factory->createRepository();
    $discovery = $factory->createDiscovery($repo);

.. include:: ../_di-note.rst

Each :class:`Puli\\Discovery\\Api\\ResourceBinding` instance may refer to more
than one :class:`Puli\\Repository\\Api\\Resource`, for example if the bound path
contains the wildcard "*". Consequently you need a double loop to access the
resources in your code:

.. code-block:: php

    $bindings = $discovery->findByType('thor/translations');

    foreach ($bindings as $binding) {
        foreach ($binding->getResources() as $resource) {
            echo $resource->getPath();
        }
    }

As a |resource provider|, you can bind resources to a binding type with the
``bind`` command of the Puli CLI:

.. code-block:: text

    $ php puli.phar bind /app/trans/*.yml thor/translations

The ``bind`` command adds the new binding to your ``puli.json``.

If a resource consumer and a resource provider are installed at the same
time, Puli makes sure that the consumer receives all resources bound to its
binding type by the provider.

In a Composer Package
---------------------

.. important::

    Before you continue, :ref:`install the Puli CLI <cli-installation>` and
    :doc:`the Repository Component <../repository/getting-started>` in your
    project.

In a Composer package, the Discovery Component is installed manually. Before
you install the component, set "minimum-stability" to "beta" in
``composer.json``:

.. code-block:: json

    {
        "minimum-stability": "beta"
    }

Install the component with Composer:

.. code-block:: text

    $ composer require puli/discovery:^1.0

As a |resource consumer|, you can define a new |binding type| with the
``type --define`` command of the Puli CLI:

.. code-block:: text

    $ php puli.phar type --define thor/translations

.. note::

    The name of a binding type must always start with the vendor name of your
    Composer package. This prevents naming collisions and makes sure that you
    *own* the type.

The ``type --define`` command adds the new binding type to your ``puli.json``.

With Puli's :class:`Puli\\Discovery\\Api\\ResourceDiscovery` class you can
access all |bindings| bound to your binding type. Each
:class:`Puli\\Discovery\\Api\\ResourceBinding` instance may refer to more
than one :class:`Puli\\Repository\\Api\\Resource`, for example if the bound path
contains the wildcard "*". Consequently you need a double loop to access the
resources in your code:

.. code-block:: php

    use Puli\Discovery\Api\ResourceDiscovery;

    class TranslationLoader
    {
        private $discovery;

        public function __construct(ResourceDiscovery $discovery)
        {
            $this->discovery = $discovery;
        }

        public function loadTranslations($catalog, $language)
        {
            $bindings = $this->discovery->findByType('thor/translations');
            $resourceName = $catalog.'.'.$language.'.yml';

            foreach ($bindings as $binding) {
                foreach ($binding->getResources() as $resource) {
                    if ($resourceName === $resource->getName()) {
                        return Yaml::parse($resource->getBody());
                    }
                }
            }

            return array();
        }
    }

You should never create :class:`Puli\\Discovery\\Api\\ResourceDiscovery`
instances in a Composer package. Instead, let the application that uses your
package create the discovery and pass it to your code. This way, every part of
the application uses the same instance and benefits of caching and other
optimizations done internally.

As a |resource provider|, you can bind resources to a binding type with the
``bind`` command of the Puli CLI:

.. code-block:: text

    $ php puli.phar bind /batman/blog/trans/*.yml thor/translations

The ``bind`` command adds the new binding to your ``puli.json``.

If a resource consumer and a resource provider are installed at the same
time, Puli makes sure that the consumer receives all resources bound to its
binding type by the provider.

Further Reading
---------------

* Read :doc:`consuming-resources` to learn more about resource consumers.
* Read :doc:`providing-resources` to learn more about resource providers.

.. _Puli's Symfony Bundle: https://github.com/puli/symfony-bundle
.. _Puli's Composer Plugin: https://github.com/puli/composer-plugin

.. |bindings| replace:: :ref:`bindings <glossary-binding>`
.. |binding type| replace:: :ref:`binding type <glossary-binding-type>`
.. |resource consumer| replace:: :ref:`resource consumer <glossary-resource-consumer>`
.. |resource provider| replace:: :ref:`resource provider <glossary-resource-provider>`
