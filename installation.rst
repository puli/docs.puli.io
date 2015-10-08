Installation
============

Requirements
------------

Puli requires PHP 5.3.9 or higher.

.. _cli-installation:

Installing the Puli CLI
-----------------------

To use Puli, you first need to install the Puli Command Line Interface (CLI).
This is usually done only once on a system.

You can install the Puli CLI in one of three ways:

As a Phar (Recommended)
~~~~~~~~~~~~~~~~~~~~~~~

Download the ``puli.phar`` from the `latest release page on GitHub`_. You can
test whether the Phar works by running:

.. code-block:: text

    $ php puli.phar

.. note::

    The leading ``$`` in the code examples is a convention that tells you that
    the example contains shell code. Type the command that follows the dollar
    sign into your terminal, but don't type the ``$`` itself.

You can either place the Phar in your project directory or somewhere in your
path (such as ``/usr/local/bin``) and chmod it to ``755``. You can rename it to
just ``puli`` to avoid typing ``puli.phar`` for every command.

The Phar can be updated with the ``self-update`` command:

.. code-block:: text

    $ php puli.phar self-update

As a Global Composer Dependency
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you prefer to install tools like PHPUnit as global Composer_ dependencies, you
can do the same for Puli. `Install Composer`_ and enter the following command in
a terminal:

.. code-block:: text

    $ composer global require puli/cli:^1.0

As a Composer Dependency
~~~~~~~~~~~~~~~~~~~~~~~~

If you want to explicitly document which version of the Puli CLI is required by
your project, you can add it to your Composer_ dependencies. `Install Composer`_
and enter the following command in a terminal:

.. code-block:: text

    $ composer require --dev puli/cli:^1.0

OSX Only: Using Homebrew
~~~~~~~~~~~~~~~~~~~~~~~~

If you are on OSX, you can also install Puli through Homebrew_:

.. code-block:: text

    $ brew install puli

Note: This command requires `Homebrew PHP to be installed`_.

Unix Only: Disable Glob Expansion
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

By default, Unix shells like Bash expand glob arguments before passing them to
the called command. Look at this short example for a demonstration:

.. code-block:: text

    # What you type
    $ command *.js

    # What the command receives by the shell
    $ command script1.js script2.js ...

If you use Puli on a Unix system, you should disable glob expansion for the
``puli`` command. If you use Bash, add the following lines to ``~/.bashrc``:

.. code-block:: bash

    # Disable glob expansion for Puli
    alias puli='set -f;puli';puli(){ command puli "$@";set +f;}

Apply the changes with the ``source`` command:

.. code-block:: text

    $ source ~/.bashrc

If you use a different shell than Bash, see `this answer on StackOverflow`_ for
instructions.

Next Steps
----------

Read :doc:`../repository/introduction` to learn how to register your resources
with Puli.

.. _latest release page on GitHub: https://github.com/puli/cli/releases
.. _this answer on StackOverflow: http://stackoverflow.com/questions/11456403/stop-shell-wildcard-character-expansion/22945024#22945024
.. _Repository: https://github.com/puli/repository
.. _Discovery: https://github.com/puli/discovery
.. _URL Generator: https://github.com/puli/url-generator
.. _CLI: https://github.com/puli/cli
.. _Composer: https://getcomposer.org
.. _Install Composer: https://getcomposer.org/doc/00-intro.md
.. _Homebrew: http://brew.sh
.. _Homebrew PHP to be installed: https://github.com/Homebrew/homebrew-php#installation

