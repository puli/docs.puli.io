Stream Wrappers
===============

Puli supports a `stream wrapper`_ that lets you access the |resources| in the
repository transparently through PHP's file functions. To register the wrapper,
call the :method:`Puli\\Repository\\StreamWrapper\\ResourceStreamWrapper::register`
method and pass the name of a URI schema and a
:class:`Puli\\Repository\\Api\\ResourceRepository` instance:

.. code-block:: php

    use Puli\Repository\StreamWrapper\ResourceStreamWrapper;

    ResourceStreamWrapper::register('puli', $repo);

After registering the stream wrapper, you can pass |Puli paths| to regular PHP
functions, prefixed by the registered URI scheme:

.. code-block:: php

    $contents = file_get_contents('puli:///acme/blog/css/style.css');

    foreach (scandir('puli:///acme/blog') as $entry) {
        // ...
    }

Performance Tweaks
------------------

The method
:method:`Puli\\Repository\\StreamWrapper\\ResourceStreamWrapper::register` needs
to be called in every request even if the stream wrapper is not used. This means
that also the repository needs to be loaded in every request, which can have
a negative performance impact on your application. To mitigate this performance
impact, you can pass a callable to
:method:`Puli\\Repository\\StreamWrapper\\ResourceStreamWrapper::register` that
loads the repository on demand:

.. code-block:: php

    class ServiceRegistry
    {
        private $repo;

        public function getRepository()
        {
            if (!$this->repo) {
                $factoryClass = PULI_FACTORY_CLASS;
                $factory = new $factoryClass();
                $this->repo = $factory->createRepository();
            }

            return $this->repo;
        }
    }

    $registry = new ServiceRegistry();

    ResourceStreamWrapper::register('puli', array($registry, 'getRepository'));

.. _stream wrapper: http://php.net/manual/en/intro.stream.php

.. |resources| replace:: :ref:`resources <glossary-resource>`
.. |Puli paths| replace:: :ref:`Puli paths <glossary-puli-path>`
