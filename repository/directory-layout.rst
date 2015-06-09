Directory Layout
================

We recommend to follow a certain directory layout in your project. This is by
no means mandatory, but it will improve your experience when working with Puli.

Most importantly, we recommend to separate PHP code and non-PHP resources into
two separate top-level directories:

.. code-block:: text

    src/
        MyService.php
        ...
    res/
        config/
            config.yml
        ...

The names of these directories don't matter -- you can name them ``source``,
``resources`` or whatever else you prefer. The important point is that the two
directories do not overlap. If the directories overlap, both the class
autoloader and the resource repository need to process unnecessary files.

Second, we recommend to use the following names for the sub-directories of the
resource directory:

.. code-block:: text

    config/
        ... configuration files ...
    public/
        css/
            ... CSS files ...
        js/
            ... Javascript files ...
        images/
            ... images ...
    trans/
        ... translation files ...
    views/
        ... templates ...

Using common names ensures a consistent user experience when referencing
resources in your project and any other Puli-enabled package:

.. code-block:: php

    // Rendering an application template with Twig
    $twig->render('/app/views/index.html');

    // Rendering a package template with Twig
    $twig->render('/acme/blog/views/post/show.html.twig');

The public resources are bundled in a directory ``public`` because this way
these resources can be easily copied to sub-directories of your public
directory:

.. code-block:: text

    /app/public/* -> /public_html/
    /acme/blog/public/* -> /public_html/blog/
    ...
