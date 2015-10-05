.. raw:: html

    <div style="margin-top: 70px; margin-bottom: 50px">

.. image:: images/logo.png
    :alt: The Puli logo.
    :align: center

.. raw:: html

    </div>

Puli: Universal Packages for PHP
================================

.. toctree::
   :hidden:

   Home <self>
   installation
   glossary

.. toctree::
   :hidden:
   :caption: Resource Repository

   Introduction <repository/introduction>
   Getting Started <repository/getting-started>
   repository/directory-layout
   repository/mapping-resources
   repository/working-with-resources
   repository/stream-wrappers

.. toctree::
   :hidden:
   :caption: Public Resources

   Introduction <url-generator/introduction>
   Getting Started <url-generator/getting-started>
   Server Configuration <url-generator/server-configuration>

.. toctree::
   :hidden:
   :caption: Discovery

   Introduction <discovery/introduction>
   Getting Started <discovery/getting-started>
   discovery/consuming-resources
   discovery/providing-resources

.. toctree::
   :hidden:
   :caption: Extensions

   Symfony Bridge <extensions/symfony-bridge>
   Symfony Bundle <extensions/symfony-bundle>
   Twig Extension <extensions/twig>

Puli_ (pronounced "poo-lee") is a universal package system for PHP. Puli aims to
replace "bundles", "plugins", "modules" and similar specialized packages of
different frameworks with one generic, framework independent solution.

Composer Packages
-----------------

From the perspective of an application developer, Composer provides two
important features when dealing with packages:

.. topic:: Installation and Dependency Resolution

    Composer installs packages in different versions together with all their
    dependencies by calling a simple console command:

    .. code-block:: text

        $ composer require batman/blog

.. topic:: Class Autoloading

    Composer generates a PSR-4 autoloader that is able to load the PHP classes
    of all installed packages. Once the autoloader is included in your code, any
    PHP class is loaded automatically when it is used for the first time:

    .. code-block:: php

        require_once __DIR__.'/vendor/autoload.php';

        // loaded by Composer's autoloader
        $post = new Batman\Blog\Post();

The Puli Package
----------------

A Puli package is a directory that contains PHP code, non-PHP files and a
``puli.json`` file. The ``puli.json`` file configures how your application and
other Puli packages access and load the contents of your package.

.. code-block:: text

    my-package/
        src/
            ... PHP files ...
        res/
            ... non-PHP files ...
        puli.json

Puli packages are typically installed with Composer_. Add a ``composer.json`` to
the package and distribute it on Packagist_. Then your package can be installed
with the ``composer`` command line utility:

.. code-block:: text

    $ composer require vendor/my-package

Features of Puli Packages
-------------------------

.. topic:: Resource Access

    Puli provides a naming convention (so called *Puli paths*) to access non-PHP
    files (YAML, XML, CSS, HTML, images and more) from a Puli package:

    .. code-block:: php

        // views/index.html.twig in the "batman/blog" package
        echo $twig->render('/batman/blog/views/index.html.twig');

    The path is resolved by reading *path mappings* from the ``puli.json`` files
    of your installed Puli packages. Authors of these packages, such as the
    author of the "batman/blog" package, map prefixes to actual directories with
    the Puli Command Line Interface (CLI):

    .. code-block:: text

        $ php puli.phar map /batman/blog res

.. topic:: Discovery

    Puli packages may act as *consumers* and as *providers* of resources and PHP
    classes. For example, a translator package (the consumer) may request
    translation files from other installed packages (the providers).

    .. raw:: html

        <p>

    .. image:: images/discovery.png
       :alt: Puli's resource discovery mechanism.
       :align: center

    .. raw:: html

        </p>

    Consumers, like our translator, give a name to the resources they want to
    load, such as "thor/translations". This name is called a *binding type*.
    Other packages (the *resource providers*) assign their translation files to
    this type. This is called *binding*. In the end, you as the user of both
    packages only need to pass Puli's :class:`Puli\\Discovery\\Api\\Discovery`
    to the ``Translator`` class:

    .. code-block:: php

        $translator = new Translator($discovery);

    The translator then loads all bound translation files from the discovery
    without any further configuration.

.. topic:: Package Overrides

    Puli packages may override other packages. For example, your application or
    a specialized theme package may replace the ``style.css`` file provided by a
    generic package "batman/blog" by a custom version:

    .. code-block:: text

        $ php puli.phar map /batman/blog/css/style.css res/css/blog/style.css

.. topic:: Resource URLs

    Puli publishes CSS files, JavaScript files and images bundled in your
    packages and generates their public URLs for you. You can pass Puli paths
    to simple utility functions to generate URLs in your PHP or HTML code:

    .. code-block:: jinja

        <img src="{{ resource_url('/batman/blog/public/logo.png') }}" />

    Puli paths can be marked public with the Puli CLI:

    .. code-block:: text

        $ php puli.phar publish /batman/blog/public localhost /blog/

    Here, we published the ``/batman/blog/public`` directory to the sub-directory
    ``/blog/`` of the server "localhost". That server must also be defined with
    the Puli CLI:

    .. code-block:: text

        $ php puli.phar server --add localhost public_html

    This command registers the server "localhost" with the directory
    ``public_html`` used as document root. Now Puli has enough information to
    generate your URLs:

    .. code-block:: html

        <img src="/blog/logo.png" />

.. topic:: Resource Installation

    If you want, you can Puli also to install your public resources on the
    server. If the server is your localhost, Puli creates simple symbolic links
    or file copies in your document root:

    .. code-block:: text

        $ php puli.phar publish --install
        Installing /batman/blog/public into public_html/blog via symlink...

    For the final release of Puli, this functionality will be moved to plugins
    for Gulp, Brunch or similar asset management tools.

Getting Started
---------------

Read :doc:`installation` to get started with Puli.

Infrastructure
--------------

Puli's core packages maintain Puli's infrastructure for you.

=================================== ==================================================== ================================================================================== ===================
Package                             Source                                               Current Version                                                                    Next Stable Release
=================================== ==================================================== ================================================================================== ===================
The Puli Manager                    `GitHub <https://github.com/puli/manager>`__         `1.0.0-beta8 <https://github.com/puli/manager/releases/tag/1.0.0-beta8>`__         late 2015
The Command Line Interface          `GitHub <https://github.com/puli/cli>`__             `1.0.0-beta8 <https://github.com/puli/cli/releases/tag/1.0.0-beta8>`__             late 2015
The Composer Plugin                 `GitHub <https://github.com/puli/composer-plugin>`__ `1.0.0-beta8 <https://github.com/puli/composer-plugin/releases/tag/1.0.0-beta8>`__ late 2015
=================================== ==================================================== ================================================================================== ===================

Components
----------

The Puli components provide the interface between your PHP code and Puli.

=================================== ==================================================== ================================================================================== ===================
Component                           Source                                               Current Version                                                                    Next Stable Release
=================================== ==================================================== ================================================================================== ===================
:doc:`repository/introduction`      `GitHub <https://github.com/puli/repository>`__      `1.0.0-beta8 <https://github.com/puli/repository/releases/tag/1.0.0-beta8>`__      late 2015
:doc:`discovery/introduction`       `GitHub <https://github.com/puli/discovery>`__       `1.0.0-beta8 <https://github.com/puli/discovery/releases/tag/1.0.0-beta8>`__       late 2015
:doc:`url-generator/introduction`   `GitHub <https://github.com/puli/url-generator>`__   `1.0.0-beta3 <https://github.com/puli/url-generator/releases/tag/1.0.0-beta3>`__   late 2015
=================================== ==================================================== ================================================================================== ===================

Extensions
----------

Puli's extensions provide integration with different third-party tools.

======================================================= =================================================== ================================================================================= ===================
Extension                                               Source                                              Current Version                                                                   Next Stable Release
======================================================= =================================================== ================================================================================= ===================
:doc:`The Symfony Bridge <extensions/symfony-bridge>`   `GitHub <https://github.com/puli/symfony-bridge>`__ `1.0.0-beta3 <https://github.com/puli/symfony-bridge/releases/tag/1.0.0-beta3>`__ late 2015
:doc:`The Symfony Bundle <extensions/symfony-bundle>`   `GitHub <https://github.com/puli/symfony-bundle>`__ `1.0.0-beta9 <https://github.com/puli/symfony-bundle/releases/tag/1.0.0-beta9>`__ late 2015
:doc:`The Twig Extension <extensions/twig>`             `GitHub <https://github.com/puli/twig-extension>`__ `1.0.0-beta7 <https://github.com/puli/twig-extension/releases/tag/1.0.0-beta7>`__ late 2015
======================================================= =================================================== ================================================================================= ===================

Authors
-------

* `Bernhard Schussek`_ a.k.a. `@webmozart`_
* `The Community Contributors`_

Contribute
----------

Contributions to Puli are very welcome!

* Report any bugs or issues you find on the `issue tracker`_.
* You can grab the source code at Puli's `Git organization`_.

If you find issues in the documentation, please let us know:

* The documentation has a dedicated `docs issue tracker`_.
* The source of the documentation is hosted at the `docs Git repository`_.

Support
-------

If you are having problems, send a mail to bschussek@gmail.com or shout out to
`@PuliPHP`_ on Twitter.

License
-------

Puli, its extensions and this documentation are licensed under the `MIT
license`_.

.. _Puli: https://github.com/puli/puli
.. _Composer: https://getcomposer.org
.. _Packagist: https://packagist.org
.. _issue tracker: https://github.com/puli/puli/issues
.. _docs issue tracker: https://github.com/puli/docs/issues
.. _Git organization: https://github.com/puli
.. _docs Git repository: https://github.com/puli/docs
.. _@webmozart: https://twitter.com/webmozart
.. _@PuliPHP: https://twitter.com/PuliPHP
.. _MIT license: LICENSE
.. _Bernhard Schussek: http://webmozarts.com
.. _The Community Contributors: https://github.com/puli/docs/graphs/contributors
