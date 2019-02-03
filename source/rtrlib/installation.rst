.. _rtrlib_installation:

Installation
============

Most Linux distributions as well as Apple macOS support RTRlib. The RTRlib
software package includes the library and basic ready-to-use command line
tools that show some of the RTRlib features.

Apple macOS
-----------

For macOS we provide a *Homebrew tap* to easily install the RTRlib.
First, setup Homebrew [#homebrew]_ and then install the RTRlib package:

.. code-block:: bash

    brew tap rtrlib/pils
    brew install rtrlib

.. rubric:: Footnotes

.. [#homebrew]  Homebrew -- http://brew.sh


Archlinux
---------

For Archlinux we maintain two PKGBUILDs in the Archlinux User Repository,
``rtrlib`` [#rtrlib]_ and ``rtrlib-git`` [#rtrlibgit]_.  ``rtrlib``
includes the latest official RTRlib release, ``rtrlib-git`` includes the
current git master.

You can either use your favourite ``aur`` helper or execute the following commands:

.. code-block:: bash

    sudo pacman --needed base-devel

    # for the latest release
    wget https://aur.archlinux.org/cgit/aur.git/snapshot/rtrlib.tar.gz
    tar xf rtrlib
    cd rtrlib

    # for the git version
    wget https://aur.archlinux.org/cgit/aur.git/snapshot/rtrlib-git.tar.gz
    tar xf rtrlib-git
    cd rtrlib-git

    # for both
    makepkg -sci


.. rubric:: Footnotes

.. [#rtrlib] https://aur.archlinux.org/packages/rtrlib/
.. [#rtrlibgit] https://aur.archlinux.org/packages/rtrlib-git/


Debian
------

RTRlib is part of the official Debian package repository since Buster
[#DebianBuster]_ and can be installed using ``apt``. The following packages
are available:

:``librtr0``: includes the basis library.
:``librtr0-dev``: includes header files etc. for developers.
:``rtr-tools``: includes basic command line tools based on RTRlib.
:``librtr0-dbgsym``: includes debugging symbols.
:``librtr-doc``: includes offline documentation.

To install the minimal set of packages required for development, execute the following command:

.. code-block:: bash

    apt install librtr0 librtr-dev

If you just want to use the RTRlib command line tools, run

.. code-block:: bash

    apt install librtr0 rtr-tools


.. rubric:: Footnotes

.. [#DebianBuster] Buster is currently in testing and scheduled for release Mid 2019.


Gentoo
------

The `FRR routing project <http://frrouting.org/>`_ maintains a gentoo
overlay [#overlay]_ that contains an ``ebuild`` for the RTRlib.  First, setup
``layman`` [#layman]_, then install rtrlib with the following commands:

.. code-block:: bash

    # If this doe not work try layman -f
    layman -a frr-gentoo
    emerge rtrlib

.. rubric:: Footnotes

.. [#overlay] https://github.com/FRRouting/gentoo-overlay
.. [#layman] https://wiki.gentoo.org/wiki/Layman


From Source
-----------

The source code repository of RTRlib includes everything that you need to
implement or run applications based on the RTRlib, and to use the RTRlib
command line tools.

The RTRlib source code consists of the following subdirectories:

- ``cmake/``      CMake modules
- ``doxygen/``    Example code and graphics used in the Doxygen documentation
- ``rtrlib/``     Header and source code files of the RTRlib
- ``tests/``      Function tests and unit tests
- ``tools/``      Contains ``rtrclient`` and ``rpki-rov``

Getting Started
"""""""""""""""

To build and install the RTRlib from source, you need the following common
software:

:``cmake`` version >= 2.6: to build the system.
:``libssh`` version >= 0.5.0: to establish SSH transport connections (optional but highly recommended).

Additional optional requirements are:

:``cmocka``: to run RTRlib unit tests
:``doxygen``: to build the RTRlib API documentation


Building
""""""""

The easiest way to get the source code is to download either the latest
RTRlib release from `https://github.com/rtrlib/rtrlib/releases/latest
<https://github.com/rtrlib/rtrlib/releases/latest>`_ or the current master
from `https://github.com/rtrlib/rtrlib/archive/master.zip
<https://github.com/rtrlib/rtrlib/archive/master.zip>`_, and then unpack:

.. code-block:: bash

    unzip rtrlib-master.zip
    cd rtrlib-master
    # or alternatively, clone the current git master
    git clone https://github.com/rtrlib/rtrlib/
    cd rtrlib

Then, build the library and command line tools using ``cmake``. We
recommend an `out-of-source` build:

.. code-block:: bash

    # inside the main RTRlib source code directory
    mkdir build && cd build
    cmake -D CMAKE_BUILD_TYPE=Release ../
    make
    sudo make install

To enable debug symbols and messages, change the ``cmake`` command to:

.. code-block:: bash

    cmake -D CMAKE_BUILD_TYPE=Debug ../

If the build command fails with any error, please consult the RTRlib README [#readme]_
and Wiki [#wiki]_, you may also join our `mailing list` [#mailinglist]_ or open
an issue on Github [#issue]_.


Additional ``cmake`` Options and Targets
"""""""""""""""""""""""""""""""""""""""

If you did not install ``libssh`` in the default directories, you can run
``cmake`` with the following parameters:

.. code-block:: bash

  -D LIBSSH_LIBRARY=<path-to-libssh.so>
  -D LIBSSH_INCLUDE=<include-directory>

To configure explicitly a directory where to place the RTRlib during
installation, you can pass the following argument to ``cmake``:

.. code-block:: bash

      -D CMAKE_INSTALL_PREFIX=<path>

For developers, we provide a pre-build API documentation online [#doxygen]_
which documents the API of the latest release. Alternatively, and if
``Doxygen`` is available on your system, you can build the documentation
locally as follows:

.. code-block:: bash

    make doc


To execute the build-in tests provided by the RTRlib package, run:

.. code-block:: bash

    make test

.. rubric:: Footnotes

.. [#readme]        README -- https://github.com/rtrlib/rtrlib/blob/master/README
.. [#wiki]          Wiki -- https://github.com/rtrlib/rtrlib/wiki
.. [#mailinglist]   Mailing list -- https://groups.google.com/forum/#!forum/rtrlib
.. [#issue]         Issue tracker -- https://github.com/rtrlib/rtrlib/issues
.. [#doxygen]       API reference -- https://rtrlib.realmv6.org/doxygen/latest




