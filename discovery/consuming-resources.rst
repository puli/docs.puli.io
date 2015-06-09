Consuming Resources
===================

|Resource consumers| are packages that load |resources| of a specific type or
format from other packages. Imagine a translation package that contains a
``Translator`` class. Translations used by the ``Translator`` can be provided
by other packages, the |resource providers|, in the form of ``*.yml`` files.

Puli supplies the infrastructure to connect resource consumers with their
providers. The consumer defines a |binding type| for the type of resource it is
interested in. The provider binds resources to this binding type. At last, the
consumer is able to load all bound resources through Puli's
:class:`Puli\\Discovery\\Api\\ResourceDiscovery`.

Defining a Binding Type
-----------------------

A binding type can be defined with the ``type --define`` command of the Puli
CLI:

.. code-block:: text

    $ php puli.phar type --define thor/translations

This command adds the new binding type to the ``puli.json`` file of your
package.

.. note::

    The name of a binding type must always start with the vendor name of your
    Composer package. This prevents naming collisions and makes sure that you
    *own* the type.

You should publish the name of your binding type in your documentation so that
resource providers for your package know which type to use.

Listing Binding Types
---------------------

You can list all defined binding types with the ``type`` command:

.. code-block:: text

    $ php puli.phar type
    The following binding types are currently enabled:

        Package: thor/translator

            Type               Description  Parameters
            thor/translations

Type Descriptions
-----------------

You should add a short description for your binding type that explains what kind
of resource the binding type expects to be bound to. A description can be set
with the ``--description`` option when adding the type:

.. code-block:: text

    $ php puli.phar type --define thor/translations \
        --description "A Yaml file with translations for the Translator class."

The description is shown in the output of the ``type`` command:

.. code-block:: text

    $ php puli.phar type
    The following binding types are currently enabled:

        Package: thor/translator

            Type               Description                            Parameters
            thor/translations  A Yaml file with translations for the
                               Translator class.

Changing a Binding Type
-----------------------

Binding types can be changed with the ``type -u`` (or ``--update``) command. The
command takes the name of the binding type as argument and one or more options
for the value that you want to change:

.. code-block:: text

    $ php puli.phar type -u thor/translations \
        --description "A Yaml file with translations for the Translator class."

Call ``type -h`` to learn about the different options supported by the ``-u``
command:

.. code-block:: text

    $ php puli.phar type -h

Deleting a Binding Type
-----------------------

Binding types can be removed with the ``type -d`` (or ``--delete``) command:

.. code-block:: text

    $ php puli.phar type -d thor/translations

This command will remove the given binding type from your ``puli.json``. Note
that you can only remove binding types defined in your own package. If you try
to remove a binding type defined by a different package installed through
Composer, the ``type -d`` command will fail.

Loading Bound Resources
-----------------------

You can load the |bindings| for your binding type with the
:method:`Puli\\Discovery\\Api\\ResourceDiscovery::findByType` method of the
:class:`Puli\\Discovery\\Api\\ResourceDiscovery`. Pass the name of your binding
Type as first and only argument:

.. code-block:: php

    $bindings = $discovery->findByType('thor/translations');

The method returns an array of :class:`Puli\\Discovery\\Api\\ResourceBinding`
instances. With :method:`Puli\\Discovery\\Api\\ResourceBinding::getResources`,
you can load the resources matched by the binding:

.. code-block:: php

    foreach ($bindings as $binding) {
        foreach ($binding->getResources() as $resource) {
            // do something with $resource...
        }
    }

Loading Bindings for a Resource
-------------------------------

You can load the bindings for a specific resource with the
:method:`Puli\\Discovery\\Api\\ResourceDiscovery::findByPath` method. This
method accepts a |Puli path| as first argument:

.. code-block:: php

    $bindings = $discovery->findByPath('/batman/blog/trans/messages.en.yml');

If you are only interested in bindings of one binding type, pass the name of the
binding type as second argument:

.. code-block:: php

    $bindings = $discovery->findByPath('/batman/blog/trans/messages.en.yml', 'thor/translations');

Binding Parameters
------------------

You can define custom parameters for a binding type. These parameters can be
used to store additional data with a binding. For example, consider that the
name of the translation catalog (here: "messages") is not taken from the file
name, but from the |binding parameter| "catalog".

You can add the parameter with the ``--param`` option when defining the binding
type:

.. code-block:: text

    $ php puli.phar type --define thor/translations --param catalog

Each binding *must* now specify a value for this parameter:

.. code-block:: text

    $ php puli.phar bind /batman/blog/trans/messages.*.yml --param catalog="blog"

Use :method:`Puli\\Discovery\\Api\\ResourceBinding::getParameterValue` on the
:class:`Puli\\Discovery\\Api\\ResourceBinding` to check the value of your
parameter:

.. code-block:: php

    $bindings = $discovery->findByType('thor/translations');

    foreach ($bindings as $binding) {
        if ('blog' === $binding->getParameterValue('catalog')) {
            // do something with $binding...
        }
    }

Optional Parameters
-------------------

Often it makes sense to define a default value for a binding parameter. You can
set a default value by setting the ``--param`` option in the form
``<param>="<default>"``:

.. code-block:: text

    $ php puli.phar type --define thor/translations --param catalog="messages"

.. note::

    The quotes (") are optional if the parameter value does not contain spaces.

Binding parameters with a default value are *optional*. If no parameter value is
set for a binding, the default value is used.

.. |Resource consumers| replace:: :ref:`Resource consumers <glossary-resource-consumer>`
.. |resource providers| replace:: :ref:`resource providers <glossary-resource-provider>`
.. |resources| replace:: :ref:`resources <glossary-resource>`
.. |binding type| replace:: :ref:`binding type <glossary-binding-type>`
.. |bindings| replace:: :ref:`bindings <glossary-binding>`
.. |binding parameter| replace:: :ref:`binding parameter <glossary-binding-parameter>`
.. |Puli path| replace:: :ref:`Puli path <glossary-puli-path>`
