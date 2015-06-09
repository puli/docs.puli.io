.. note::

    For performance reasons, Puli services such as ``$factory`` or ``$repo``
    should be created only once per application. Instead of storing them in
    global variables, it is usually nicer to use a Dependency Injection
    Container for creating the services on demand. A simple Dependency Injection
    Container for small projects is Pimple_.

.. _Pimple: http://pimple.sensiolabs.org
