.. _doc_routinator_configuration:

Configuration
=============

Routinator can take its configuration from a file. You can specify
such a configuration file via the ``-c`` option. If you don’t, Routinator
will check if there is a file ``$HOME/.routinator.conf`` and if it exists,
use it. If it doesn’t exist and there is no ``-c`` option, default values
are used.

The configuration file is a TOML file. Its entries are named similarly to
the command line options. Details about the available entries and there
meaning can be found in the manual page. In addition, a complete sample
configuration file showing all the default values can be found in the
repository at `etc/routinator.conf <https://github.com/NLnetLabs/routinator/blob/master/etc/routinator.conf>`_.
