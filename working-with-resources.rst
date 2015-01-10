Working with Resources
======================

This guide explains how to use the resources returned by the Puli repository.
If you do not have a working repository, read :doc:`getting-started`.

If you don't know what Puli is or why you should use it, read :doc:`at-a-glance`
first.

Accessing Resources
-------------------

You can access individual resources in the repository with the method
:method:`Puli\\Repository\\Api\\ResourceRepository::get`:

.. code-block:: php

    $resource = $repo->get('/css/style.css');

The :method:`Puli\\Repository\\Api\\ResourceRepository::get` method accepts
the path of a resource and returns a :class:`Puli\\Repository\\Api\\Resource\\Resource`.

If you want to retrieve multiple resources at once, use
:method:`Puli\\Repository\\Api\\ResourceRepository::find`. This method accepts a
glob pattern and returns a
:class:`Puli\\Repository\\Resource\\Api\\ResourceCollection`:

.. code-block:: php

    foreach ($repo->find('/css/*')->getPaths() as $path) {
        echo $path;
    }

    // => /css/reset.css
    // => /css/style.css

You can check whether a resource exists by passing its path to
:method:`Puli\\Repository\\Api\\ResourceRepository::contains`:

.. code-block:: php

    if ($repo->contains('/css/style.css')) {
        // ...
    }

Like :method:`Puli\\Repository\\Api\\ResourceRepository::find`, this method
also accepts glob patterns. If you pass a glob, the method will return ``true``
only if at least one resource matched the pattern.

Resources
---------

The :method:`Puli\\Repository\\Api\\ResourceRepository::get` method returns
:class:`Puli\\Repository\\Api\\Resource\\Resource` instances. This interface
provides access to the name and the repository path of the resource:

.. code-block:: php

    $resource = $repo->get('/css/style.css');

    echo $resource->getName();
    // => style.css

    echo $resource->getPath();
    // => /css/style.css

Resources don't necessarily have to be located on the filesystem. But those
that do implement :class:`Puli\\Repository\\Resource\\Api\\Resource\\FilesystemResource`,
which lets you access the filesystem path with
:method:`Puli\\Repository\\Resource\\Api\\Resource\\FilesystemResource::getFilesystemPath`:

.. code-block:: php

    $resource = $repo->get('/css/style.css');

    echo $resource->getFilesystemPath();
    // => /path/to/res/assets/css/style.css

Resources that have a body - such as files - implement
:class:`Puli\\Repository\\Resource\\Api\\Resource\\BodyResource`. This interface
lets you access the body with
:method:`Puli\\Repository\\Resource\\Api\\Resource\\BodyResource::getBody`:

.. code-block:: php

    $resource = $repo->get('/css/style.css');

    $css = $resource->getBody();

Child Resources
---------------

Resources support child resources. One prime example is a filesystem directory
which may contain other directories and files.

You can access the children of a resource with the methods
:method:`Puli\\Repository\\Api\\Resource\\Resource::getChild`,
:method:`Puli\\Repository\\Api\\Resource\\Resource::hasChild` and
:method:`Puli\\Repository\\Api\\Resource\\Resource::listChildren`:

.. code-block:: php

    $resource = $directory->getChild('style.css');

    if ($directory->hasChild('style.css')) {
        // ...
    }

    foreach ($directory->listChildren() as $name => $resource) {
        // ...
    }

Metadata
--------

Resources support the method
:method:`Puli\\Repository\\Api\\Resource\\Resource::getMetadata` which returns
a :class:`Puli\\Repository\\Api\\Resource\\ResourceMetadata` instance. This
interface gives access to various metadata of a resource. For example, you can
use :method:`Puli\\Repository\\Api\\Resource\\ResourceMetadata::getModificationTime`
to access the UNIX timestamp of the resource's last modification. This is useful
for caching:

.. code-block:: php

    $resource = $repo->get('/css/style.css');

    if ($resource->getMetadata()->getModificationTime() > $cacheTimestamp) {
        // refresh cache
    }

Resource Collections
--------------------

When you fetch multiple resources from the repository, they are returned
within a :class:`Puli\\Repository\\Resource\\Api\\ResourceCollection`
instance. Resource collections offer convenience methods for accessing the names
and the paths of all contained resources at once:

.. code-block:: php

    $resources = $repo->get('/css/*.css');

    print_r($resources->getNames());
    // Array
    // (
    //     [0] => reset.css
    //     [1] => style.css
    // )

    print_r($resources->getPaths());
    // Array
    // (
    //     [0] => /css/reset.css
    //     [1] => /css/style.css
    // )

Resource collections are traversable, countable and support
:phpclass:`ArrayAccess`. When you still need the collection as array, call
:method:`Puli\\Repository\\Resource\\Api\\ResourceCollection::toArray`:

.. code-block:: php

    $array = $resources->toArray();

.. _Puli: https://github.com/puli/puli
