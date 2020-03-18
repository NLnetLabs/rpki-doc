.. _doc_routinator_manpage:

Manual Page
===========

Name
----
:command:`routinator` - RPKI relying party software

Synopsis
--------
:command:`routinator` :option:`-b base-dir` :option:`-r repository-dir`
:option:`-t tal-dir` :option:`-x exceptions-file [-x exceptions-file [...]]`
:option:`--strict` :option:`--disable-rsync` :option:`--rsync-command=args`
:option:`--rsync-timeout=seconds` :option:`--disable-rrdp`
:option:`--rrdp-timeout=seconds` :option:`--rrdp-connect-timeout=seconds`
:option:`--rrdp-local-addr=addr` :option:`--rrdp-root-cert=path`
:option:`--rrdp-proxy=uri` :option:`--dirty`
:option:`--validation-threads=count` :option:`-v | -vv | -q | -qq` :option:`-h`
:option:`-V` :command:`command` :option:`args`

:command:`routinator` :option:`options` :command:`init` :option:`-f`

:command:`routinator` :option:`options` :command:`vrps` :option:`-o output-file`
:option:`-f format` :option:`-n` :option:`-a asn` :option:`-p prefix`

:command:`routinator` :option:`options` :command:`validate` :option:`-n`
:option:`-j` :option:`-a asn` :option:`-p prefix`

:command:`routinator` :option:`options` :command:`server`
:option:`--rtr addr:port [...]` :option:`--http addr:port [...]`
:option:`--listen-systemd` :option:`--refresh seconds` :option:`--retry seconds`
:option:`--expire seconds` :option:`--history count`

:command:`routinator` :option:`options` :command:`update`

:command:`routinator` :command:`man` :option:`-o file`

Description
-----------
Routinator collects and processes Resource Public Key Infrastructure
(RPKI) data. It validates the Route Origin Attestations contained in
the data and makes them available to your BGP routing workflow.

It can either run in one-shot mode outputting a list of validated route
origins in various formats or as a server for the RPKI-to-Router (RTR)
protocol that routers often implement to access the data, or via HTTP.

These modes and additional operations can be chosen via commands. For
the available commands, see **COMMANDS** below.

Options
-------
The available options are:

.. option:: -c path, --config=path

    Provides the path to a file containing basic configuration. If this option
    is not given, Routinator will try to use ``$HOME/.routinator.conf`` if that
    exists. If that doesn't exist, either, default values for the options as
    described here are used.

    See CONFIGURATION FILE below for more information on the format and contents
    of the configuration file.


.. option:: -b dir, --base-dir=dir

    Specifies the base directory  to  keep  status  information  in. Unless overwritten by the -r or -t options, the local repository will be kept in the sub-directory repository and the  TALs  will
    be kept in the sub-directory tals.

    If omitted, the base directory defaults to $HOME/.rpki-cache.

Testing
-------

.. option:: --paginate

 Pipe all message output into :command:`less` (or if set, to the command
 referred in :envvar:`MODULES_PAGER` variable) if error output stream is a
 terminal. See also :envvar:`MODULES_PAGER` section.

 .. only:: html

    .. versionadded:: 4.1

.. option:: --silent, -s

 Turn off error, warning and informational messages. :command:`module` command
 output result is not affected by silent mode.

 .. only:: html

    .. versionadded:: 4.3
       :option:`--silent`/:option:`-s` support was dropped on version `4.0`
       but reintroduced starting version `4.3`.

.. option:: --starts-with, -S

 On :subcmd:`avail` sub-command, return modules whose name starts with search
 query string.
