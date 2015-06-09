Mapping Resources
=================

|Puli paths| can be mapped to files or directories with the ``map`` command of
the Puli CLI:

.. code-block:: text

    $ php puli.phar map /acme/blog res

The first argument is a |path prefix|, followed by one or more paths in your
project. By convention, the path prefix should equal the name of your Composer
package with an additional leading slash ("/"). The resulting |path mapping| is
added to the ``puli.json`` file of your package.

.. tip::

    If you develop an application that is not used as Composer dependency by
    other packages, use ``/app`` as path prefix.

You cannot just map directories, but also individual files. This is helpful if
you need to cherry-pick files from specific locations:

.. code-block:: text

    $ php puli.phar map /acme/blog/css/reset.css shared/reset.css

Listing Mappings
----------------

The current path mappings can be displayed by calling the ``map`` command
without arguments:

.. code-block:: text

    $ php puli.phar map
    The following path mappings are currently enabled:

        Package: puli/acme-blog

            Puli Path   Real Path(s)
            /acme/blog  res

The path displays all path mappings currently found in your project. If you
want to display just the path mappings of your own package (the |root package|),
pass the ``--root`` option. If you want to display just the path mappings of a
package with a specific name, use the ``--package`` option. With ``map -h``, you
can learn more about the ``map`` command:

.. code-block:: text

    $ php puli.phar map -h

Listing Mapped Files
--------------------

Now that we mapped paths to the repository, it would be nice to know which
|resources| the repository actually contains. You can use ``ls`` -- just like
the ``ls`` command on UNIX -- to list the resources in the repository:

.. code-block:: text

    $ php puli.phar ls
    acme
    $ php puli.phar ls /acme/blog/config
    config.yml  config-dev.yml  doctrine

You can also print the resources as tree with ``tree``:

.. code-block:: text

    $ php puli.phar tree
    /
    ├── acme
    │   └── blog
    │       └── config
    │           └── ...
    └── app
        └── ...

This command prints the whole repository by default. You can also pass the Puli
path of an individual resource if you want to print just a part of the
repository:

.. code-block:: text

    $ php puli.phar tree /acme/blog/config
    /acme/blog/config
    ├── config.yml
    ├── config-dev.yml
    ├── doctrine
    │   ├── Acme.Blog.Post.dcm.xml
    │   └── ...
    └── ...

At last, use ``find`` to list resources according to different criteria:

.. code-block:: text

    $ php puli.phar find --name *.yml
    FileResource /acme/blog/config/config.yml
    FileResource /acme/blog/config/config-dev.yml

Pass ``-h`` to find out more about each command's arguments and options:

.. code-block:: text

    $ php puli.phar find -h

Changing a Mapping
------------------

You can add paths to a path mapping with the ``map -u`` (or ``--update``)
command:

.. code-block:: text

    $ php puli.phar map -u /acme/blog --add assets
    $ php puli.phar map
    The following path mappings are currently enabled:

        Package: puli/acme-blog

            Puli Path   Real Path(s)
            /acme/blog  res, assets

When a path mapping contains multiple files or directories, later mappings
*override* earlier mappings. That means, the file ``/acme/blog/css/style.css``
will be looked for first in ``assets/css/style.css``, then in
``res/css/style.css``.

Likewise, paths can be removed from a path mapping with the ``--remove`` option:

.. code-block:: text

    $ php puli.phar map -u /acme/blog --remove assets
    $ php puli.phar map
    The following path mappings are currently enabled:

        Package: puli/acme-blog

            Puli Path   Real Path(s)
            /acme/blog  res

Deleting a Mapping
------------------

Path mappings can be removed completely with ``map -d`` (or ``--delete``):

.. code-block:: text

    $ php puli.phar map -d /acme/blog

Referencing Other Packages
--------------------------

Sometimes it is necessary to map path prefixes to files or directories in other
Composer packages. A typical use case is when you use packages that don't
contain a ``puli.json`` file.

Use the prefix ``@<vendor/package>:`` to reference the install path of another
package:

.. code-block:: text

    $ php puli.phar map /acme/theme @acme/theme:res

The example above maps the Puli path ``/acme/theme`` to the ``res`` directory of
the "acme/theme" package.

Further Reading
---------------

* Read :doc:`working-with-resources` to learn how to use the resources returned
  by the resource repository.

.. _Puli: https://github.com/puli/puli

.. |resources| replace:: :ref:`resources <glossary-resource>`
.. |Puli paths| replace:: :ref:`Puli paths <glossary-puli-path>`
.. |path prefix| replace:: :ref:`path prefix <glossary-path-prefix>`
.. |path mapping| replace:: :ref:`path mapping <glossary-path-mapping>`
.. |root package| replace:: :ref:`root package <glossary-root-package>`
