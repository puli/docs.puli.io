Mapping Resources
=================

This guide explains how to map resources with Puli_. Puli should be installed
already. If it is not, follow the instructions in :doc:`getting-started`.

If you don't know what Puli is or why you should use it, read
:doc:`at-a-glance` first.

Mapping Resources
-----------------

Puli paths can be mapped to files or directories with the ``puli map`` command:

.. code-block:: text

    $ puli map /acme/blog res

The first argument is a Puli path, followed by one or more paths in your
project. By convention, Puli paths should contain your vendor and package name
as top-level directories.

.. tip::

    If you develop an application that is not going to be shared, use ``/app``
    as top-level directory of your Puli paths.

You can access the mapped files through the
:class:`Puli\\Repository\\Api\\ResourceRepository` instance created in
:doc:`getting-started`:

.. code-block:: php

    // res/views/index.html
    echo $repo->get('/acme/blog/views/index.html')->getBody();

You cannot just map directories, but also individual files. This is helpful if
you need to cherry-pick files from specific locations:

.. code-block:: text

    $ puli map /acme/blog/css/reset.css shared/reset.css

Listing Mappings
----------------

The current mappings can be displayed by typing ``puli map`` without arguments:

.. code-block:: text

    $ puli map
    /acme/blog res

This command just displays the mappings in your project. If you want to list
all mappings in all installed packages, type ``puli map -a``:

.. code-block:: text

    $ puli map -a
    acme/blog
    /acme/blog res

    acme/common
    /acme/common res

Listing Mapped Files
--------------------

Now that we mapped paths to the repository, it would be nice to know which files
the repository actually contains. You can use ``puli ls`` -- just like the
``ls`` command on UNIX -- to list the resources in the repository:

.. code-block:: text

    $ puli ls
    acme
    $ puli ls /acme/blog/config
    config.yml  config-dev.yml  doctrine

You can also print the resources as tree with ``puli tree``:

.. code-block:: text

    $ puli tree
    /
    ├── acme
    │   └── blog
    │       └── config
    │           └── ...
    └── app
        └── ...

This command prints the whole repository by default. You can also pass the path
of an individual resource if you want to print just a part of the repository:

.. code-block:: text

    $ puli tree /acme/blog/config
    /acme/blog/config
    ├── config.yml
    ├── config-dev.yml
    ├── doctrine
    │   ├── Acme.Blog.Post.dcm.xml
    │   └── ...
    └── ...

At last, use ``puli find`` to list resources according to different criteria:

.. code-block:: text

    $ puli find *.yml
    FileResource /acme/blog/config/config.yml
    FileResource /acme/blog/config/config-dev.yml

Type ``puli <command> -h`` to find out more about each command's arguments and
options. Typing ``puli <command> --help`` or ``puli help <command>`` will bring
up the full documentation of each command.

Modifying Mappings
------------------

You can add a path to a mapping by prefixing that path with "+":

.. code-block:: text

    $ puli map /acme/blog +assets
    $ puli map
    /acme/blog res, assets

When a mapping contains multiple files or directories, later mappings *override*
earlier mappings. That means, the file ``/acme/blog/css/style.css`` will be
looked for first in ``assets/css/style.css``, then in ``res/css/style.css``.

The same mapping could have been achieved without the "+" prefix by repeating
all existing mapped paths:

.. code-block:: text

    $ puli map /acme/blog res assets
    $ puli map
    /acme/blog res, assets

Remove a path from the mapping by prefixing that path with "-":

.. code-block:: text

    $ puli map /acme/blog -assets
    $ puli map
    /acme/blog res

Mappings can be removed completely with ``puli map -d``:

.. code-block:: text

    $ puli map -d /acme/blog

Referencing Other Packages
--------------------------

Sometimes it is necessary to map paths that are located in other packages. A
typical use case is when you use packages that don't contain resource mappings
by themselves.

Use the prefix ``@package-name:`` to reference the install path of that package:

.. code-block:: text

    $ puli map /acme/theme @acme/theme:res

The example above maps the Puli path ``/acme/theme`` to the ``res`` directory in
the "acme/theme" package.

Further Reading
---------------

* Read :doc:`working-with-resources` to learn how to use the resources returned
  by the resource repository.

.. _Puli: https://github.com/puli/puli
