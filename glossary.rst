Glossary
========

.. _glossary-binding:

**Binding**
    Assigns one or more resources to a binding type.

.. _glossary-binding-parameter:

**Binding Parameter**
    Additional, named data stored with a binding. Needs to be defined by the
    binding type. Can be optional or required.

.. _glossary-binding-type:

**Binding Type**
    A name for a semantic type of resources, like "thor/translations". Should
    start with the vendor namespace of the package that defines the name.

.. _glossary-child-resource:

**Child Resource**
    A resource nested in another resource.

.. _glossary-package:

**Package**
    A logical grouping of files in a directory. Typically installed with
    Composer.

.. _glossary-path-mapping:

**Path Mapping**
    Maps a path prefix to a file or directory in a Composer package.

.. _glossary-path-prefix:

**Path Prefix**
    A prefix of a Puli path, like ``/batman/blog``.

.. _glossary-public-resource:

**Public Resource**
    A resource that is published in the document root of a web server and
    accessible in the internet.

.. _glossary-puli-aware-package:

**Puli-Enabled Package**
    A package that contains a ``puli.json`` file in its root directory.

.. _glossary-puli-path:

**Puli Path**
    The path of a resource in the resource repository, like
    ``/batman/blog/views/index.html.twig``.

.. _glossary-resource:

**Resource**
    A file or directory, typically XML, YAML, HTML, CSS, an image etc.

.. _glossary-resource-consumer:

**Resource Consumer**
    A package that defines a binding type and uses resources bound to this type.

.. _glossary-resource-provider:

**Resource Provider**
    A package that contains resources bound to a binding type of a resource
    Consumer.

.. _glossary-root-package:

**Root Package**
    The package for the current project. Any Puli project has exactly one root
    package and any number of package dependencies (non-root packages).

.. _glossary-url-format:

**URL Format**
    A printf string used to generate URLs of public resources, like
    ``https://example.com/%s``.

.. _glossary-uuid:

**UUID**
    Short for Universally Unique Identifier. A random 128-bit value used to
    identify bindings and other distributed objects managed by Puli.

.. _glossary-web-server:

**Web Server**
    Represents a physical web server in Puli. Typically has a URL format and a
    document root.
