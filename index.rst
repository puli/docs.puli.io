.. raw:: html

    <div style="margin-top: 70px; margin-bottom: 50px">

.. image:: images/logo.png
    :alt: The Puli logo.
    :align: center

.. raw:: html

    </div>

Puli: Plug 'n Play Packages for Composer
========================================

.. toctree::
   :hidden:

   Home <self>
   features
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
   :caption: Resource Discovery

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

Puli_ (pronounced "poo-lee") is a PHP tool that enables you to create Plug 'n
Play packages for Composer_. These packages are completely independent of any
PHP framework. Use them with your favorite framework or without. The choice is
yours.

Resources?
----------

*Resources*, in Puli's language, are all files that are not PHP, such as XML,
YAML, CSS, JavaScript, images and so on. What PHP's autoloader is to your PHP
code, Puli is to any other file.

How does it work?
-----------------

Puli puts a ``puli.json`` file into each Puli-enabled package. This file
contains metadata about the resources of the package and can be edited through
Puli's Command Line Interface (CLI). Your PHP code loads Puli resources through
the Puli Components.

Features
--------

.. topic:: Automatic Resource Discovery

    With Puli, your installed Composer packages recognize each other
    automatically. You no longer need to configure packages to find each other's
    files. Puli does it for you.

.. topic:: Load Resources Anywhere

    Puli provides a framework-agnostic naming convention to access files in
    Composer packages. With this convention, you can easily load files from any
    installed package. Independent of your framework.

.. topic:: Publish Package Resources

    Puli publishes your package's public resources, such as CSS files or images,
    to your web server and generates their URLs for you. Want to serve images
    from a Content Delivery Network? You can do it.

.. topic:: Override Package Resources

    With Puli, you can easily override files in other Composer packages. Want to
    customize a HTML, CSS or a configuration file? With Puli, all you need is to
    call a simple console command.

:doc:`Read more about Puli's features » <features>`

Installation
------------

Read :doc:`installation` to get started with Puli.

Infrastructure
--------------

Puli's core packages maintain Puli's infrastructure for you.

=================================== ==================================================== ================================================================================== ===================
Package                             Source                                               Current Version                                                                    Next Stable Release
=================================== ==================================================== ================================================================================== ===================
The Puli Manager                    `GitHub <https://github.com/puli/manager>`__         `1.0.0-beta5 <https://github.com/puli/manager/releases/tag/1.0.0-beta5>`__         late 2015
The Command Line Interface          `GitHub <https://github.com/puli/cli>`__             `1.0.0-beta5 <https://github.com/puli/cli/releases/tag/1.0.0-beta5>`__             late 2015
The Composer Plugin                 `GitHub <https://github.com/puli/composer-plugin>`__ `1.0.0-beta5 <https://github.com/puli/composer-plugin/releases/tag/1.0.0-beta5>`__ late 2015
=================================== ==================================================== ================================================================================== ===================

Components
----------

The Puli components provide the interface between your PHP code and Puli.

=================================== ==================================================== ================================================================================== ===================
Component                           Source                                               Current Version                                                                    Next Stable Release
=================================== ==================================================== ================================================================================== ===================
:doc:`repository/introduction`      `GitHub <https://github.com/puli/repository>`__      `1.0.0-beta5 <https://github.com/puli/repository/releases/tag/1.0.0-beta5>`__      late 2015
:doc:`discovery/introduction`       `GitHub <https://github.com/puli/discovery>`__       `1.0.0-beta5 <https://github.com/puli/discovery/releases/tag/1.0.0-beta5>`__       late 2015
:doc:`url-generator/introduction`   `GitHub <https://github.com/puli/url-generator>`__   `1.0.0-beta1 <https://github.com/puli/url-generator/releases/tag/1.0.0-beta1>`__   late 2015
=================================== ==================================================== ================================================================================== ===================

Extensions
----------

Puli's extensions provide integration with different third-party tools.

======================================================= =================================================== ================================================================================= ===================
Extension                                               Source                                              Current Version                                                                   Next Stable Release
======================================================= =================================================== ================================================================================= ===================
:doc:`The Symfony Bridge <extensions/symfony-bridge>`   `GitHub <https://github.com/puli/symfony-bridge>`__ `1.0.0-beta2 <https://github.com/puli/symfony-bridge/releases/tag/1.0.0-beta2>`__ late 2015
:doc:`The Symfony Bundle <extensions/symfony-bundle>`   `GitHub <https://github.com/puli/symfony-bundle>`__ `1.0.0-beta6 <https://github.com/puli/symfony-bundle/releases/tag/1.0.0-beta6>`__ late 2015
:doc:`The Twig Extension <extensions/twig>`             `GitHub <https://github.com/puli/twig-extension>`__ `1.0.0-beta4 <https://github.com/puli/twig-extension/releases/tag/1.0.0-veta4>`__ late 2015
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
.. _issue tracker: https://github.com/puli/puli/issues
.. _docs issue tracker: https://github.com/puli/docs/issues
.. _Git organization: https://github.com/puli
.. _docs Git repository: https://github.com/puli/docs
.. _@webmozart: https://twitter.com/webmozart
.. _@PuliPHP: https://twitter.com/PuliPHP
.. _MIT license: LICENSE
.. _Bernhard Schussek: http://webmozarts.com
.. _The Community Contributors: https://github.com/puli/docs/graphs/contributors
