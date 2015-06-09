Web Server Configuration
========================

Before Puli can generate URLs for your |public resources|, you need to add a
|web server| to the Puli configuration and publish your resources there.

Adding a Web Server
-------------------

A web server can be added with the ``server --add`` command of the Puli CLI:

.. code-block:: text

    $ php puli.phar server --add localhost public_html

This command adds a new server to your ``puli.json``. The command receives a
name for the server as first argument. Here we chose "localhost", but you can
use any name you like. The second argument is the path to the document root of
the server.

Listing Web Servers
-------------------

You can list all configured web servers with the ``server`` command:

.. code-block:: text

    $ php puli.phar server
    Server Name  Installer  Document Root  URL Format
    localhost    symlink    public_html    /%s

The command returns a list of servers and their current configuration. In this
example, our configuration contains one server named "localhost". The document
root of the server is the ``public_html`` directory. Resources are installed
there using symbolic links. Finally, the format string ``/%s`` is used to
generate URLs for the resources published to this server. You will learn more
about |URL formats| in `Custom URL Formats`_.

Changing a Web Server
---------------------

The configuration of a web server can be changed with the ``server -u`` (or
``--update``) command:

.. code-block:: text

    $ php puli.phar server -u localhost --document-root public_html

The first argument is the name of the server you want to change. You can pass
additional options with the updated options. Run ``server -h`` to learn more
about the supported options and their usage:

.. code-block:: text

    $ php puli.phar server -h

Deleting a Web Server
---------------------

A web server can be deleted with the ``server -d`` (or ``--delete``) command:

.. code-block:: text

    $ php puli.phar server -d localhost

Publishing Resources
--------------------

To publish resources to a server, use the ``publish`` command:

.. code-block:: text

    $ php puli.phar publish /app/public localhost

The command stores the created mapping in your ``puli.json`` file. The first
argument is the Puli path of the resource(s) you want to publish. The second
is the name of the server you want to publish the resources in.

By default, the resources are published in the document root of the web server.
If you want to publish the resources in a sub-directory instead, pass the path
to the directory in the third argument:

.. code-block:: text

    $ php puli.phar publish /batman/blog/public localhost /blog

Listing Public Resources
------------------------

You can display all public resources with the ``publish`` command:

.. code-block:: text

    $ php puli.phar publish
    The following resources are published:

        Server localhost
        Location:   public_html
        Installer:  symlink
        URL Format: /%s

            4f1f14 /app/public         /
            d1a9d5 /batman/blog/public /blog

    Use "puli publish --install" to install the resources on your servers.

Every public resource is identified by a |UUID|. This UUID can be used to update
or delete the resource.

Unpublishing a Resource
-----------------------

To unpublish a resource, call ``publish -d`` (or ``--delete``) with the UUID of
the resource in question:

.. code-block:: text

    $ php puli.phar publish -d d1a9d5

Installing Resources
--------------------

The ``publish`` command does not actually move your public resources to the web
server. This is done by ``publish --install``:

.. code-block:: text

    $ php puli.phar publish --install
    Installing /app/public into public_html via symlink...
    Installing /batman/blog/public into public_html/blog via symlink...

By default, Puli tries to create symbolic links in the document of your web
server. If that's not possible, it will fall back to copying your files.

Custom URL Formats
------------------

By default, web servers use the URL format ``/%s`` to generate URLs for
resources published to that server. The format is a simple sprintf_-string: The
``%s`` in the format is replaced by the path of the resource relative to the
document root of the server.

For example, if you publish your ``/app/public`` directory to the document root
of your server, Puli will generate the URL ``/images/logo.png`` for the Puli
path ``/app/public/images/logo.png``.

You can change the URL format with the ``--url-format`` option when adding or
changing a server:

.. code-block:: text

    $ php puli.phar server -u localhost --url-format http://example.com/%s

There are two major use cases for changing the default URL format:

* When you install your resources on a different server, you need to include
  the domain name in every URL.
* By appending a query string (like ``?v2``) to your generated URLs you can
  implement a very simple cache invalidation mechanism for your resources.

.. _sprintf: http://php.net/manual/en/function.sprintf.php

.. |public resources| replace:: :ref:`public resources <glossary-public-resource>`
.. |web server| replace:: :ref:`web server <glossary-web-server>`
.. |URL formats| replace:: :ref:`URL formats <glossary-url-format>`
.. |UUID| replace:: :ref:`UUID <glossary-uuid>`
