Web Assets
==========

Some resources - such as templates or configuration files - are needed by the
web server only. Others - like CSS files and images - need to be placed in a
public directory, where browsers can download them. We'll call these files
*web assets* here.

Puli's `Asset Plugin`_ takes care of:

* installing assets to their public destination (public directories, CDNs, ...);
* generating the URLs for these assets.

Read this guide to learn everything about handling web resources with Puli.

Installation
------------

You can install the plugin with Composer_:

.. code-block:: text

    $ composer require puli/asset-plugin:~1.0

Before the plugin can be used, it needs to be enabled with the Puli CLI:

.. code-block:: text

    $ puli plugin install Puli\\AssetPlugin\\Api\\AssetPlugin

Install Targets
---------------

The public location for installing resources is called an *install target* in
Puli's language. Puli supports virtually any kind of install target, such as:

* the document root of your own web server
* the document root of another web server
* a Content Delivery Network (CDN)

Install targets store three pieces of information:

* their location (a directory path, a URL, ...)
* the used installer (symlink, copy, ftp, rsync, ...)
* their URL format

The URL format is used to generate URLs for the resources installed in the
target. The default format is ``/%s``, but you could set it to more elaborate
values such as ``http://cdn.example.com/path/%s?v3``.

Install targets can be created with the ``puli target add`` command. We need to
create at least one target:

.. code-block:: text

    $ puli target add local public_html

With this command, we added a target named "local" that points to the
``public_html`` directory in our project.

Installing Web Assets
---------------------

Now that the install target is set up, we can map resources to paths in the
target:

.. code-block:: text

    $ puli asset map /app/public /
    $ puli asset map /acme/blog/{css,js} /blog

The ``puli asset map`` command takes two parameters:

* a path (or glob) of a Puli resource;
* a path in the install target.

With the above commands, we mapped the ``/app/public`` directory to the root
directory of our install target -- hence ``public_html`` -- and the directories
``/acme/blog/css`` and ``/acme/blog/js`` to the sub-directory
``public_html/blog``.

We can display the current mappings by running ``puli asset``:

.. code-block:: text

    The following web assets are currently enabled:

        Target default (alias of: local)
        Location:   public_html
        Installer:  symlink
        URL Format: /%s

            54b31b /app/public         /
            d7c042 /acme/blog/{css,js} /blog

    Use "puli asset install" to install the assets in their targets.

At last, we will install the resources by running ``puli asset install``:

.. code-block:: text

    $ puli asset install
    Installing /app/public into public_html via symlink...
    Installing /acme/blog/css into public_html/blog/css via symlink...
    Installing /acme/blog/js into public_html/blog/js via symlink...

The assets are installed in the ``public_html`` directory by creating
symbolic links. We will learn how to change the installation method in the
`Installers`_ section later in this guide.

Generating Resource URLs
------------------------

Now that the assets are published, we need to let browsers know where the
resources can be downloaded. With `Puli's Twig Extension`_, you can use the
``asset_url()`` function, which accepts a resource path and returns the
URL of the resource:

.. code-block:: jinja

    {# /images/header.png #}
    <img src="{{ asset_url('/app/public/images/header.png') }}" />

You can also pass a path relative to the Puli path of your template:

.. code-block:: jinja

    <img src="{{ asset_url('../images/header.png') }}" />

If you want to generate resource URLs in your own libraries, use the
:class:`Puli\\AssetPlugin\\Api\\UrlGenerator\\AssetUrlGenerator` that
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
Let's run ``puli asset`` again to get a better understanding:

.. code-block:: text

    The following web assets are currently enabled:

        Target default (alias of: local)
        Location:   public_html
        Installer:  symlink
        URL Format: /%s

            54b31b /app/public         /
            d7c042 /acme/blog/{css,js} /blog

    Use "puli asset install" to install the assets in their targets.

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

If you publish your assets in a sub-directory of your document root, you
need to adjust the URL format correspondingly:

.. code-block:: text

    $ puli target add local public_html/assets --url-format /assets/%s

We told Puli to publish the assets of the target in the ``assets``
sub-directory. Likewise, we had to adjust the URL format to ``/assets/%s``, so
that the generated URLs use the ``/assets/`` prefix.

Asset Versioning
~~~~~~~~~~~~~~~~

Another use case for changing the URL format is asset versioning. Let's add the
query string ``?v1`` to the URLs of all resources in our target:

.. code-block:: text

    $ puli target add local public_html --url-format /%s?v1

If you release a new version of your application, you only have to update the
URL format to force all browsers to reload your assets instead of using the
cached version:

.. code-block:: text

    $ puli target add local public_html --url-format /%s?v2

Installers
----------

Puli supports different asset installers:

================= ==============================================
Installer         Description
================= ==============================================
symlink (default) Creates asset symlinks in a target directory
copy              Copies assets to a target directory
================= ==============================================

You can select the used installer when creating the install target:

.. code-block:: text

    $ puli target add local public_html --installer copy

In future versions of Puli, more installers (ftp, rsync, ...) will be added.
You can also create a custom installer by implementing the
:class:`Puli\\AssetPlugin\\Api\\Installer\\ResourceInstaller` interface.
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
    $ puli target add staging /var/www/staging/static --installer copy
    $ puli target add prod /var/www/prod/static --installer copy

Puli sets the first added target as default target. The default target is marked
with a star ``*`` in the output of ``puli target``:

.. code-block:: text

    $ puli target
    * dev     symlink public_html             /%s
      staging copy    /var/www/staging/static /%s
      prod    copy    /var/www/prod/static    /%s

All resources are installed in the default target by default (unless you
passed a specific target to ``puli asset map``, as you will learn in the next
section). When you move your application to the staging server, you can change
the default target before installing your resources:

.. code-block:: text

    $ puli target set-default staging

When you run ``puli asset install``, your assets will be installed in the
staging target now.

Parallel Install Targets
------------------------

In the previous section, we created multiple install targets, but only used
one at a time. You can also assign your assets to different install targets
at the same time. This is useful, for example, if you want to serve some
assets from your own server and others from a CDN:

.. code-block:: text

    $ puli target add local public_html
    $ puli target add cdn ssh://cdn.example.com \
    >     --installer rsync \
    >     --url-format http://cdn.example.com/%s

When you map your web assets, select their target with the ``--target`` option
of the ``puli asset map`` command:

.. code-block:: text

    $ puli asset map /app/public/{css,js} / --target local
    $ puli asset map /app/public/images /images --target cdn

When you run ``puli asset install``, Puli will install the assets in the
configured targets:

.. code-block:: text

    $ puli asset install
    Installing /app/public/css into public_html/css via symlink...
    Installing /app/public/js into public_html/js via symlink...
    Installing /app/public/images into ssh://cdn.example.com/images via rsync...

Since you specified a custom URL format for the "cdn" target, Puli will also
generate the correct resource URLs:

.. code-block:: php

    // /css/style.css
    $urlGenerator->generateUrl('/app/public/css/style.css');

    // http://cdn.example.com/images/header.png
    $urlGenerator->generateUrl('/app/public/images/header.png');

.. _Asset Plugin: https://github.com/puli/web-resource-plugin
.. _Composer: https://getcomposer.org
.. _Puli's Twig Extension: https://github.com/puli/twig-extension
