Providing Resources
===================

|Resource providers| are packages that contain |resources| that should be loaded
by a specific |resource consumer|. Imagine a package with a list of translations
stored in a ``messages.en.yml`` file that is supposed to be loaded by a specific
translator. If the translation package, the resource consumer, defines a
|binding type|, you can pass your translation file to that type. Puli takes care
of supplying your file to the translator.

Adding a Binding
----------------

A |binding| can be added with the ``bind`` command of the Puli CLI:

.. code-block:: text

    $ php puli.phar bind /batman/blog/trans/*.yml thor/translations

The command takes a |Puli path| or a glob as first argument. The second argument
is the name of the binding type you want to bind your resources to. The created
binding is added to the ``puli.json`` file of your package.

The ``bind`` command fails if the binding type is not found. You should
*always* install the packages defining the binding types you are using
("require" or "require-dev" in ``composer.json``) so that Puli can validate
whether your binding is specified correctly. If, for some reason, that's not
possible, use ``-f`` (or ``--force`` when adding your binding:

.. code-block:: text

    $ php puli.phar bind -f /batman/blog/trans/*.yml thor/translations

Listing Bindings
----------------

Bindings can be listed with the ``bind`` command without arguments:

.. code-block:: text

    $ php puli.phar bind
    The following bindings are currently enabled:

    Package: batman/blog

        UUID    Glob                      Type
        bb5a07  /batman/blog/trans/*.yml  thor/translations

Each binding has a |UUID| that is used to identify the binding.

Changing a Binding
------------------

Bindings can be changed with the ``bind -u`` (or ``--update``) command. The
command takes the UUID of the binding together with one or more options with the
values that you want to change:

.. code-block:: text

    $ php puli.phar bind -u bb5a07 --query /batman/blog/trans/messages.*.yml

Call ``bind -h`` to learn about the different options supported by the ``-h``
command:

.. code-block:: text

    $ php puli.phar bind -h

Deleting a Binding
------------------

Bindings can be removed with the ``bind -d`` (or ``--delete``) command:

.. code-block:: text

    $ php puli.phar bind -d bb5a07

This command removes the given binding from your ``puli.json``. You can only
remove bindings defined by your package. If you try to remove a binding of a
different package, the command will fail.

Disabling a Binding
-------------------

You can't delete bindings defined by other packages, but you can disable them
with the ``bind --disable`` command:

.. code-block:: text

    $ php puli.phar bind --disable bb5a07

This command marks the matching binding as disabled in your ``puli.json``.
Disabled bindings are treated like deleted bindings, except that they are not
physically removed.

Disabled bindings only work in the ``puli.json`` of the |root package|. For any
other installed package, disabled bindings are ignored.

Enabling a Binding
------------------

A disabled binding can be enabled with the ``bind --enable`` command:

.. code-block:: text

    $ php puli.phar bind --enable bb5a07

This command simply reverses the effects of ``bind --disable`` in your
``puli.json`` file.

Binding Parameters
------------------

If the binding type you are binding to specifies |binding parameters|, you can
set values for these parameters with the ``--param`` option of the ``bind``
command. The option must be passed in the format ``<param>="<value>"``:

.. code-block:: text

    $ php puli.phar bind /batman/blog/trans/*.yml thor/translations --param catalog="blog"

.. note::

    The quotes (") are optional if the parameter value does not contain spaces.

You can learn which parameters are supported/required by the binding type by
calling the ``type`` command:

.. code-block:: text

    $ php puli.phar type
    The following binding types are currently enabled:

        Package: thor/translator

            Type               Description                            Parameters
            thor/translations  A Yaml file with translations for the  catalog
                               Translator class.

.. |resource consumer| replace:: :ref:`resource consumer <glossary-resource-consumer>`
.. |Resource providers| replace:: :ref:`Resource providers <glossary-resource-provider>`
.. |resources| replace:: :ref:`resources <glossary-resource>`
.. |binding type| replace:: :ref:`binding type <glossary-binding-type>`
.. |binding| replace:: :ref:`binding <glossary-binding>`
.. |Puli path| replace:: :ref:`Puli path <glossary-puli-path>`
.. |UUID| replace:: :ref:`UUID <glossary-uuid>`
.. |root package| replace:: :ref:`root package <glossary-root-package>`
.. |binding parameters| replace:: :ref:`binding parameters <glossary-binding-parameter>`
