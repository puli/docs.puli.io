.. |nbsp| unicode:: 0xA0
   :trim:

Resource Discovery
==================

This guide explains how to map Puli_ resources to binding types and how to
find these resources later on.

If you don't know what Puli is or why you should use it, read :doc:`at-a-glance`
first.

Why?
----

Frequently, libraries support specific types of files. For example, the
`Doctrine ORM`_ is able to load entity mappings from XML files:

.. code-block:: xml

    <!-- res/config/doctrine/MyProject.User.dcm.xml -->
    <doctrine-mapping ...>
        <entity name="MyProject\User">
            <field name="name" type="string" />
        </entity>
    </doctrine-mapping>

Registering this file -- and all other such files in your project *and* its
installed packages -- with Doctrine's classes requires significant effort.

Resource Binding and Discovery
------------------------------

Puli supports a very simple binding mechanism to solve this problem. Packages
like Doctrine can define *binding types* with the command ``puli type define``:

.. code-block:: text

    # doctrine/orm developer
    $ puli type define doctrine/xml-mapping

.. attention::

    This guide describes **two different user perspectives**:

    * The author of a library that **consumes** resources
      (here: |nbsp| "doctrine/orm").
    * A developer that **provides** resources to that library
      (here: |nbsp| the developer of the "acme/blog" package)

    The code samples always start with a comment describing their perspective.

.. note::

    As you see, the type "doctrine/xml-mapping" is prefixed with the vendor name
    of the package that defines the type ("doctrine/orm"). This practice is
    enforced in Puli to prevent naming collisions. Also, the vendor prefix makes
    it clear who is consuming resources bound to the type.

Any package that contains Doctrine mappings *binds* these mappings to the type:

.. code-block:: text

    # acme/blog developer
    $ puli bind /acme/blog/config/doctrine/*.xml doctrine/xml-mapping

Doctrine then fetches all resources bound to the type from Puli's
:class:`Puli\\Discovery\\Api\\ResourceDiscovery` and processes them:

.. code-block:: php

    // doctrine/orm developer
    foreach ($discovery->find('doctrine/xml-mapping') as $binding) {
        foreach ($binding->getResources() as $resource) {
            // load $resource...
        }
    }

.. tip::

    Read :doc:`getting-started` if you forgot how to obtain the
    :class:`Puli\\Discovery\\Api\\ResourceDiscovery` instance.

Binding Parameters
------------------

Puli allows you to define parameters that can be set when binding resources to
a type. For example, assume that Doctrine wants to support the following
parameters for the "doctrine/xml-mapping" type:

* "namespace": A common namespace shared by all entities described in the
  mapping.
* "alias": A short and memorable alias for that namespace.

You can use the ``--param`` option of the ``puli type define`` command to add
parameters:

.. code-block:: text

    # doctrine/orm developer
    $ puli type define doctrine/xml-mapping \
        --param namespace=null \
        --param alias=null

.. note::

    The trailing backslash "\\" allows to add a line break in your terminal
    without submitting the command.

Parameters can either be optional or required:

* ``--param <name>`` adds a required parameter;
* ``--param <name>=<default-value>`` adds an optional parameter with the given
  default value.

When you bind resources to a type, you can use the same syntax to set values
for the parameters:

.. code-block:: text

    # acme/blog developer
    $ puli bind /acme/blog/config/doctrine/*.xml doctrine/xml-mapping \
        --param namespace="Acme\Blog\Entity\" \
        --param alias="blog:"

The parameters can be queried from the
:class:`Puli\\Discovery\\Api\\Binding\\ResourceBinding` instances returned by
:method:`Puli\\Discovery\\Api\\ResourceDiscovery::find`:

.. code-block:: php

    // doctrine/orm developer
    foreach ($discovery->find('doctrine/xml-mapping') as $binding) {
        $namespace = $binding->getParameter('namespace');
        $alias = $binding->getParameter('alias');

        foreach ($binding->getResources() as $resource) {
            // load $resource...
        }
    }

Type Descriptions
-----------------

To help the users of your type, you should add human-readable descriptions
that describe what the type and its parameters do:

.. code-block:: text

    # doctrine/orm developer
    $ puli type define doctrine/xml-mapping \
        --description "An XML mapping loaded by the Doctrine ORM" \
        --param namespace="Acme\Blog\Entity\" \
        --description "A common namespace shared by all mapped entities" \
        --param alias="blog:" \
        --description "An alias for the namespace"

The description is displayed when listing the installed types with ``puli type``:

.. code-block:: text

    # acme/blog developer
    $ puli type
    Enabled binding types:

        doctrine/orm
        doctrine/xml-mapping An XML mapping loaded by the Doctrine ORM
                             (namespace=null, alias=null)

    Use "puli bind <resource> <type>" to bind a resource to a type.

Enabled and Disabled Bindings
-----------------------------

When you install packages that bind resources to a binding type, these bindings
are not automatically enabled. Consider that you, the "acme/blog" developer,
install the package "awesome/comment" which contains Doctrine mappings for a
``Comment`` entity. These bindings are displayed by the command ``puli bind``:

.. code-block:: text

    # acme/blog developer
    $ puli bind
    Enabled bindings:

        acme/blog
        1fb5b8 /acme/blog/config/doctrine/*.xml doctrine/xml-mapping

    Bindings that are neither enabled nor disabled:
     (use "puli bind --enable <uuid>" to enable)

        awesome/comment
        8fb9c8 /awesome/comment/config/doctrine/*.xml doctrine/xml-mapping

Until you enable the binding, Doctrine will never know it is there. You have
full control over what bindings you want to enable for any installed package.

Let's enable the binding now:

.. code-block:: text

    # acme/blog developer
    $ puli bind --enable 8fb9c8
    $ puli bind
    Enabled bindings:

        acme/blog
        1fb5b8 /acme/blog/config/doctrine/*.xml doctrine/xml-mapping

        awesome/comment
        8fb9c8 /awesome/comment/config/doctrine/*.xml doctrine/xml-mapping

That's all you need to know about resource discovery with Puli. As you see,
this mechanism is very simple, yet very powerful.

.. _Puli: https://github.com/puli/puli
.. _Doctrine ORM: http://www.doctrine-project.org/projects/orm.html
