Web Resources
=============

Some resources - such as templates or configuration files - are needed by the
web server only. Others - like CSS files and images - need to be placed in a
public directory, where browsers can download them. We'll call these files
*web resources* here.

Puli's `Web Resource Plugin`_ takes care of:

* installing web resources to their public destination (public directories,
  CDNs, ...);
* generating the URLs for these resources.

Read this guide to learn everything about handling web resources with Puli.

Installation
------------

You can install the plugin with Composer_:

.. code-block:: text

    $ composer require puli/web-resource-plugin:~1.0@beta

Before the plugin can be used, it needs to be enabled with the Puli CLI:

.. code-block:: text

    $ puli plugin install Puli\\WebResourcePlugin\\Api\\WebResourcePlugin

Installing Web Resources
------------------------

Puli installs web resources to paths in so-called *install targets*. An install
target is any publicly accessible location, such as:

* a public directory of the web-server itself;
* a public directory of a different web-server;
* a public directory of a Content Delivery Network (CDN).

Since we don't have an install target yet, we will create a new one:

.. code-block:: text

    $ puli target add local public_html

With this command, we added a target named "local" that points to the
``public_html`` directory in our project. For now, this is all we need. You
will learn more about install targets later in this guide.

Next, we will map resources to paths in the target:

.. code-block:: text

    $ puli web add /app/public /
    $ puli web add "/acme/blog/{css,js}" /blog

The ``puli web add`` command takes two parameters:

* a path (or glob) of a Puli resource;
* a path in the install target.

With the above commands, we mapped the ``/app/public`` directory to the root
directory of our install target - hence ``public_html/``, and the directories
``/acme/blog/css`` and ``/acme/blog/css`` to the sub-directory
``public_html/blog``.

We can display the current mappings by running ``puli web``:

.. code-block:: text

    The following web resources are currently enabled:

        Target default (alias of: local)
        Location:   public_html
        Installer:  symlink
        URL Format: /%s

            54b31b /app/public         /
            d7c042 /acme/blog/{css,js} /blog

    Use "puli web install" to install the resources in their targets.

At last, we will install the resources by running ``puli web install``:

.. code-block:: text

    $ puli web install
    Installing /app/public into public_html via symlink...
    Installing /acme/blog/css into public_html/blog/css via symlink...
    Installing /acme/blog/js into public_html/blog/js via symlink...

The resources are installed in the ``public_html`` directory by creating
symbolic links. We will learn how to change the installation method in the
`Installers`_ section later in this guide.

Generating Resource URLs
------------------------

Now that the resources are published, we need to let browsers know where the
resources can be downloaded. With `Puli's Twig Extension`_, you can use the
``resource_url()`` function, which accepts a resource path and returns the
URL of the resource:

.. code-block:: jinja

    {# /images/header.png #}
    <img src="{{ resource_url('/app/public/images/header.png') }}" />

You can also pass a path relative to the Puli path of your template:

.. code-block:: jinja

    <img src="{{ resource_url('../images/header.png') }}" />

If you want to generate resource URLs in your own libraries, use the
:class:`Puli\\WebResourcePlugin\\Api\\UrlGenerator\\ResourceUrlGenerator` that
is returned by the Puli factory:

.. code-block:: php

    $urlGenerator = $factory->createUrlGenerator($discovery);

    // /images/header.png
    $urlGenerator->generateUrl('/app/public/images/header.png');

Read :doc:`getting-started` for more information about creating the Puli
factory.

Custom URL Formats
------------------

By default, Puli generates URLs with the format ``/%s``, where ``%s`` is
replaced by the resource's path relative to the install target.
Let's run ``puli web`` again to get a better understanding:

.. code-block:: text

    The following web resources are currently enabled:

        Target default (alias of: local)
        Location:   public_html
        Installer:  symlink
        URL Format: /%s

            54b31b /app/public         /
            d7c042 /acme/blog/{css,js} /blog

    Use "puli web install" to install the resources in their targets.

Since ``/app/public`` is mapped to the root of the install target, Puli
generates the URL ``/images/header.png`` for the resource
``/app/public/images/header.png``.

Absolute URLs
~~~~~~~~~~~~~

If you want Puli to generate absolute URLs instead, pass the ``--url-format``
option when creating the target with ``puli target add``:

.. code-block:: text

    $ puli target add local public_html --url-format http://example.com/%s

Now Puli generates the URL ``http://example.com/images/header.png`` for the
header image.

Sub-Directories of the Document Root
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you publish your resources in a sub-directory of your document root, you
need to adjust the URL format correspondingly:

.. code-block:: text

    $ puli target add local public_html/assets --url-format /assets/%s

We told Puli to publish the resources of the target in the ``assets``
sub-directory. Likewise, we had to adjust the URL format to ``/assets/%s``, so
that the generated URLs use the ``/assets/`` prefix.

Resource Versioning
~~~~~~~~~~~~~~~~~~~

Another use case for changing the URL format is resource versioning. Let's
add the query string ``?v1`` to the URLs of all resources in our target:

.. code-block:: text

    $ puli target add local public_html --url-format /%s?v1

If you release a new version of your application, you only have to update the
URL format to force all browsers to reload your resources instead of using the
cached version:

.. code-block:: text

    $ puli target add local public_html --url-format /%s?v2

Installers
----------

Puli supports different resource installers:

================= ==============================================
Installer         Description
================= ==============================================
symlink (default) Publishes resources by creating symbolic links
copy              Publishes resources using hard copies
================= ==============================================

You can select the used installer when creating the install target:

.. code-block:: text

    $ puli target add local public_html --installer copy

In future versions of Puli, more installers (ftp, rsync, ...) will be added.
You can also create a custom installer by implementing the
:class:`Puli\\WebResourcePlugin\\Api\\Installer\\ResourceInstaller` interface.
With ``puli installer add``, the installer can be added to your Puli project:

.. code-block:: text

    $ puli installer add rsync My\\Installer\\RsyncInstaller

This command accepts an installer name and the installer class as arguments.
Now you can use the installer in your install target:

.. code-block:: text

    $ puli target add local public_html --installer rsync

Switching the Install Target
----------------------------

You can create more than one install target in your Puli project. For example,
consider that you need different install targets for development, the staging
server and the production server:

.. code-block:: text

    $ puli target add dev public_html
    $ puli target add staging /var/www/staging/public_html --installer copy
    $ puli target add prod /var/www/prod/public_html --installer copy

Puli sets the first added target as default target. The default target is marked
with a star ``*`` in the output of ``puli target``:

.. code-block:: text

    $ puli target
    * dev     symlink public_html             /%s
      staging copy    /var/www/staging/static /%s
      prod    copy    /var/www/prod/static    /%s

All resources are installed in the default target by default (unless you
passed a specific target to ``puli web add``, as you will learn in the next
section). When you move your application to the staging server, you can change
the default target before installing your resources:

.. code-block:: text

    $ puli target set-default staging

When you run ``puli web install``, your resources will be installed in the
staging target now.

Parallel Install Targets
------------------------

In the previous section, we created multiple install targets, but only used
one at a time. You can also assign your resources to different install targets
at the same time. This is useful, for example, if you want to serve some
resources from your own server and others from a CDN:

.. code-block:: text

    $ puli target add local public_html
    $ puli target add cdn ssh://cdn.example.com \
    >     --installer rsync \
    >     --url-format http://cdn.example.com/%s

When you map your web resources, select their target with the ``--target``
option of the ``puli web add`` command:

.. code-block:: text

    $ puli web add /app/public/{css,js} / --target local
    $ puli web add /app/public/images /images --target cdn

When you run ``puli web install``, Puli will install the resources in the
configured targets:

.. code-block:: text

    $ puli web install
    Installing /app/public/css into public_html/css via symlink...
    Installing /app/public/js into public_html/js via symlink...
    Installing /app/public/images into ssh://cdn.example.com/images via rsync...

Since you specified a custom URL format for the cdn target, Puli will also
generate the correct resource URLs:

.. code-block:: php

    // /css/style.css
    $urlGenerator->generateUrl('/app/public/css/style.css');

    // http://cdn.example.com/images/header.png
    $urlGenerator->generateUrl('/app/public/images/header.png');

.. _`Web Resource Plugin`: https://github.com/puli/web-resource-plugin
.. _Composer: https://getcomposer.org
.. _Puli's Twig Extension: https://github.com/puli/twig-extension
