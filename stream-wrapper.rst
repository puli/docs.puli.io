Stream Wrapper
==============

This guide explains how to use Puli paths with PHP's file functions.

If you don't know what Puli is or why you should use it, read :doc:`at-a-glance`
first.

Registering a Scheme
--------------------

Puli supports a `stream wrapper`_ that lets you access the contents of the
repository transparently through PHP's file functions. To register the wrapper,
call the :method:`Puli\\Repository\\StreamWrapper\\ResourceStreamWrapper::register`
method and pass the name of a URI schema and a
:class:`Puli\\Repository\\Api\\ResourceRepository` instance:

.. code-block:: php

    use Puli\Repository\StreamWrapper\ResourceStreamWrapper;

    ResourceStreamWrapper::register('puli', $repo);

After registering the stream wrapper, you can use regular PHP functions to
access the files and directories in the repository, as long as you prefix them
with the registered URI scheme:

.. code-block:: php

    $contents = file_get_contents('puli:///acme/blog/css/style.css');

    foreach (scandir('puli:///acme/blog') as $entry) {
        // ...
    }

Performance Tweaks
------------------

Unfortunately, the stream wrapper cannot be loaded on demand. The method
:method:`Puli\\Repository\\StreamWrapper\\ResourceStreamWrapper::register` needs
to be called in every request even if the stream wrapper is not used.

To mitigate the performance impact of the registration, you can pass a callable
that loads the repository on demand:

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

.. _Puli: https://github.com/puli/puli
.. _stream wrapper: http://php.net/manual/en/intro.stream.php
