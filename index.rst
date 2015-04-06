Welcome to Puli's documentation!
================================

.. toctree::
   :hidden:

   at-a-glance
   getting-started
   mapping-resources
   working-with-resources
   web-assets
   discovery
   stream-wrapper
   components
   extensions

Puli_ (pronounced "poo-lee") manages the files, directories and other resources
of your project in a filesystem-like repository. With the Puli CLI, you map
*Puli paths* to paths in your project:

.. code-block:: text

    $ puli map app res

Files in these paths can then be easily accessed in your PHP code:

.. code-block:: php

    // res/views/index.html.twig
    $html = $twig->render('/app/views/index.html.twig');

Read :doc:`at-a-glance` to learn more about what Puli is and why you need it.

Authors
-------

* `Bernhard Schussek`_ a.k.a. `@webmozart`_
* `The Community Contributors`_

Installation
------------

Follow the :doc:`getting-started` guide to install Puli in your project.

Contents
--------

The documentation contains the following sections:

* :doc:`at-a-glance`
* :doc:`getting-started`
* :doc:`mapping-resources`
* :doc:`working-with-resources`
* :doc:`web-assets`
* :doc:`discovery`
* :doc:`stream-wrapper`

The appendix contains further useful information:

* :doc:`components`
* :doc:`extensions`

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
.. _issue tracker: https://github.com/puli/puli/issues
.. _docs issue tracker: https://github.com/puli/docs/issues
.. _Git organization: https://github.com/puli
.. _docs Git repository: https://github.com/puli/docs
.. _@webmozart: https://twitter.com/webmozart
.. _@PuliPHP: https://twitter.com/PuliPHP
.. _MIT license: LICENSE
.. _Bernhard Schussek: http://webmozarts.com
.. _The Community Contributors: https://github.com/puli/docs/graphs/contributors
