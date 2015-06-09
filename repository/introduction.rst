The Repository Component
========================

Puli's `Repository Component`_ provides a framework-agnostic naming convention
for accessing resources in your project and your installed Composer packages.
*Resources*, in Puli, are all files that are not PHP, such as XML, YAML, CSS,
JavaScript, images and so on.

The Problem
-----------

At the moment, you are probably accessing files through the ``__DIR__`` constant:

.. code-block:: php

    echo file_get_contents(__DIR__.'/../../res/views/index.html.twig');

Paths constructed like this become very long. Also, these paths break when you
move the PHP file and make refactoring harder.

Many frameworks solve this problem by looking for files in a fixed directory:

.. code-block:: php

    // res/views/index.html.twig
    echo load_file('views/index/html.twig');

This solution works well for simple applications, but breaks if you want to
load resources from *different* resource directories, like your installed
Composer packages. Frameworks solve this problem by supporting aliases for
the resource directories of different packages:

.. code-block:: php

    // vendor/acme/blog/res/views/index.html.twig
    echo load_file('acme-blog:views/index/html.twig');

Unfortunately, every framework has a different naming convention for these
aliases. Puli solves this by introducing a universal naming convention that
works with any framework -- and even without.

How It Works
------------

The Repository Component manages resources in a repository that looks very
much like a Unix filesystem.  Each resource is accessible through a *Puli path*:

.. code-block:: php

    // res/views/index.html.twig
    echo $repo->get('/app/views/index.html.twig')->getBody();

Puli finds this file by loading *path mappings* from the ``puli.json`` files in
your application and all installed Composer package. Such a mapping can be
added with the Puli CLI:

.. code-block:: text

    $ php puli.phar map /app res

In this example, you mapped the *path prefix* ``/app`` to the directory ``res``
in your application. Puli now knows that any file with the prefix ``/app``
should be looked for in the ``res`` directory.

.. image:: ../images/repository.png
   :alt: Puli's Repository Component.
   :align: center

Behind the scenes, the Puli CLI builds a database from the mappings in the
different ``puli.json`` files of your project. This database can be accessed
from your PHP code through a :class:`Puli\\Repository\\Api\\ResourceRepository`
instance. The format of the database depends on the concrete
:class:`Puli\\Repository\\Api\\ResourceRepository` implementation.

Getting Started
---------------

Read :doc:`getting-started` to learn how to install and use the Repository
Component in your project.

.. _Repository Component: https://github.com/puli/repository
