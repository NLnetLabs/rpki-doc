.. _doc_routinator_manpage:

Manual Page
===========

:command:`routinator` - RPKI relying party software

:Date:   2019-11-29
:Author: NLnet Labs
:Copyright: Public domain
:Version: 0.6.4

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
the available commands, see `Commands`_ below.

Options
-------

The available options are:

.. option:: -c path, --config=path

    Provides the path to a file containing basic configuration. If this option
    is not given, Routinator will try to use :file:`$HOME/.routinator.conf` if
    that exists. If that doesn't exist, either, default values for the options
    as described here are used.

    See `Configuration File`_ below for more information on the format and
    contents of the configuration file.

.. option:: -b dir, --base-dir=dir

    Specifies the base directory to keep status information in. Unless
    overwritten by the :option:`-r` or :option:`-t` options, the local
    repository will be kept in the sub-directory repository and the TALs will
    be kept in the sub-directory :file:`tals`.

    If omitted, the base directory defaults to :file:`$HOME/.rpki-cache`.

.. option:: -r dir, --repository-dir=dir

      Specifies the directory to keep the local repository in. This is
      the place where Routinator stores the RPKI data it has collected
      and thus is a copy of all the data referenced via the trust anchors.

.. option:: -t dir, --tal-dir=dir

      Specifies the directory containing the trust anchor locators (TALs) to
      use. Trust anchor locators are the starting points for collecting and
      validating RPKI data. See `Trust Anchor Locators`_ for more information
      on what should be present in this directory.

.. option:: -x file, --exceptions=file

      Provides the path to a local exceptions file. The option can be used
      multiple times to specify more than one file to use. Each file is a JSON
      file as described in :rfc:`8416`. It lists both route origins that should
      be filtered out of the output as well as origins that should be added.

.. option:: --strict

      If this option is present, the repository will be validated in strict
      mode following the requirements laid out by the standard documents very
      closely. With the current RPKI repository, using this option will lead to
      a rather large amount of invalid route origins and should therefore not be
      used in practice.

      See `Relaxed Validation`_ below for more information.

.. option:: --disable-rsync

      If this option is present, rsync is disabled and only RRDP will be used.

.. option:: --rsync-command=command

      Provides the command to run for rsync. This is only the command itself. If
      you need to provide options to rsync, use the ``rsync-args``
      configuration file setting instead.

      If this option is not given, Routinator will simply run rsync and hope
      that it is in the path.

.. option:: --rsync-timeout=seconds

      Sets the number of seconds an rsync command is allowed to run before it
      is terminated early. This protects against hanging rsync commands that
      prevent Routinator from continuing. The default is 300 seconds which
      should be long enough except for very slow networks.

.. option:: --disable-rrdp

      If this option is present, RRDP is disabled and only rsync will be used.

.. option:: --rrdp-timeout=seconds

      Sets the timeout in seconds for any RRDP-related network operation, i.e.,
      connects, reads, and writes. If this option is omitted, the default
      timeout of 30 seconds is used. Set the option to 0 to disable the timeout.

.. option:: --rrdp-connect-timeout=seconds

      Sets the timeout in seconds for RRDP connect requests. If omitted, the
      general timeout will be used.

.. option:: --rrdp-local-addr=addr

      If present,  sets the local address that the RRDP client should bind to
      when doing outgoing requests.

.. option:: --rrdp-root-cert=path

      This option provides a path to a file that contains a certificate in PEM
      encoding that should be used as a trusted certificate for HTTPS server
      authentication. The option can be given more then once.

      Providing this option does not disable the set of regular HTTPS
      authentication trust certificates.

.. option:: --rrdp-proxy=uri

      This option provides the URI of a proxy to use for all HTTP connections
      made by the RRDP client. It can be either an HTTP or a SOCKS URI. The
      option can be given multiple times in which case proxies are tried in the
      given order.

.. option:: --dirty

      If this option is present, unused files and directories will not be
      deleted from the repository directory after each validation run.

.. option:: --validation-threads=count

      Sets the number of threads to distribute work to for validation. Note that
      the current processing model validates trust anchors all in one go, so you
      are likely to see less than that number of threads used throughout the
      validation run.

.. option:: -v, --verbose

      Print more information. If given twice, even more information is printed.

      More specifically, a single :option:`-v` increases the log level from the
      default of warn to info, specifying it more than once increases it to
      debug.

.. option:: -q, --quiet

      Print less information. Given twice, print nothing at all.

      A single :option:`-q` will drop the log level to error. Repeating
      :option:`-q` more than once turns logging off completely.

.. option:: --syslog

      Redirect logging output to syslog.

      This option is implied if a command is used that causes Routinator to run
      in daemon mode.

.. option:: --syslog-facility=facility

      If logging to syslog is used, this option can be used to specify the
      syslog facility to use. The default is daemon.

.. option:: --logfile=path

      Redirect logging output to the given file.

.. option:: -h, --help

      Print some help information.

.. option:: -V, --version

      Print version information.

Commands
--------

Routinator provides a number of operations around the local RPKI repository.
These can be requested by providing different commands on the command line.

:command:`init`
    Prepares the local repository directories and the TAL directory for running
    Routinator.  Specifically,  makes sure the local repository directory
    exists, and creates the TAL directory and fills it with the TALs of the five
    RIRs.

    For more information about TALs, see `Trust Anchor Locators`_ below.

    .. option:: -f

           Forces installation of the TALs even if the TAL directory already
           exists.

    .. option:: --accept-arin-rpa

           Before you can use the ARIN TAL, you need to agree to the ARIN
           Relying Party Agreement (RPA). You can find it at
           https://www.arin.net/resources/manage/rpki/rpa.pdf and explicitly
           agree to it via this option. This explicit agreement is necessary in
           order to install the ARIN TAL.

    .. option:: --decline-arin-rpa

           If, after reading the ARIN Relying Party Agreement, you decide you do
           not or cannot agree to it, this option allows you to skip
           installation of the ARIN TAL. Note that this means Routinator will
           not have access to any information published for resources assigned
           under ARIN.


:command:`vrps`
    This command requests that Routinator update the local repository and then
    validate the Route Origin Attestations in the repository and output the
    valid route origins, which are also known as Validated ROA Payload or VRPs,
    as a list.

    .. option:: -o file, --output=file

              Specifies the output file to write the list to. If this option
              is missing or file is - the list is printed to standard output.

    .. option:: -f format, --format=format

           The output format to use. Routinator currently supports the
           following formats:

           csv
                  The list is formatted as lines of comma-separated values of
                  the prefix in slash notation, the maximum prefix length,
                  the autonomous system number, and an abbreviation for the
                  trust anchor the entry is derived from. The latter is the
                  name of the TAL file without the extension *.tal*.

                  This is the default format used if the :option:`-f` option
                  is missing.

           csvext
                  An extended version of csv each line contains these
                  comma-separated values: the rsync URI of the ROA the line
                  is taken from (or "N/A" if it isn't from a ROA), the
                  autonomous system number, the prefix in slash notation, the
                  maximum prefix length, the not-before date and not-after
                  date of the validity of the ROA.

                  This format was used in the RIPE NCC RPKI Validator version
                  1. That version produces one file per trust anchor. This is
                  not currently supported by Routinator -- all entries will
                  be in one single output file.

           json
                  The list is placed into a JSON object with a single
                  element *roas* which contains an array of objects with
                  four elements each:  The autonomous system number of the
                  network authorized to originate a prefix in *asn*, the
                  prefix in slash notation in *prefix*, the maximum prefix
                  length of the announced route in *maxLength*, and the
                  trust anchor from which the authorization was derived in
                  *ta*. This format is identical to that produced by the RIPE
                  NCC RPKI Validator except for different naming of the
                  trust anchor. Routinator uses the name of the TAL file
                  without the extension *.tal* whereas the RIPE NCC Validator
                  has a dedicated name for each.

           openbgpd
                  Choosing this format causes Routinator to produce a roa-
                  set configuration item for the OpenBGPD configuration.

           rpsl
                  This format produces a list of RPSL objects with the
                  authorization in the fields *route*, *origin*, and
                  *source*. In addition, the fields *descr*, *mnt-by*,
                  *created*, and *last-modified*, are present with more or
                  less meaningful values.

           summary
                  This format produces a summary of the content of the RPKI
                  repository. For each trust anchor, it will print the number
                  of verified ROAs and VRPs. Note that this format does not
                  take filters into account. It will always provide numbers
                  for the complete repository.

           none
                  This format produces no output whatsoever.

    .. option:: --noupdate

           The repository will not be updated before producing the list.

    .. option:: --complete

           If any of the rsync commands needed to update the repository
           failed, complete the operation but provide exit status 2. If
           this option is not given, the operation will complete with exit
           status 0 in this case.

    .. option:: -a asn, --filter-asn=asn

           Only output VRPs for the given ASN. The option can be given mul-
           tiple times,  in which case VRPs for all provided ASNs are pro-
           vided. ASNs can be given with or without the prefix AS.

    .. option:: -p prefix, --filter-prefix=prefix

           Only output VRPs with an address prefix that covers the given
           prefix, i.e., whose prefix is equal to or less specific than the
           given prefix. This will include VRPs regardless of their ASN and
           max length.  In other words, the output will include all VRPs
           that need to be considered when deciding whether an announcement
           for the prefix is RPKI valid or invalid.

           The option can be given multiple times, in which case VRPs for
           all prefixes are provided. It can also be combined with one or
           more ASN filters. Then all matching VRPs are included. That is,
           filters combine as "or" not "and."


:command:`validate`
       This command can be used to perform RPKI route origin validation for a
       route announcement.  Routinator will determine whether the provided
       announcement is RPKI valid, invalid, or not found.

       .. option:: -a asn, --asn=asn

              The AS number of the autonomous system that originated the route
              announcement. ASNs can be given with or without the prefix AS.

       .. option:: -p prefix, --prefix=prefix

              The address prefix the route announcement is for.

       .. option:: -j, --json

              A detailed analysis on the reasoning behind the validation is
              printed in JSON format including lists of the VPRs that caused
              the particular result.   If this option is omitted, Routinator
              will only print the determined state.

       .. option:: -n, --noupdate

              The repository will not be updated before performing validation.

       .. option:: --complete

              If any of the rsync commands needed to update the repository
              failed, complete the operation but provide exit status 2. If this
              option is not given, the operation will complete with exit status
              0 in this case.


:command:`server`
       This command causes Routinator to act as a server for the RPKI-to-
       Router (RTR) and HTTP protocols. In this mode, Routinator will read all
       the TALs  (See `Trust Anchor Locators`_ below) and will stay attached to
       the terminal unless the :option:`-d` option is given.

       The server will periodically update the local repository, every ten
       minutes by default, notify any clients of changes, and let them fetch
       validated data. It will not, however, reread the trust anchor locators.
       Thus, if you update them, you will have to restart Routinator.

       You can provide a number of addresses and ports to listen on for RTR
       and HTTP through command line options or their configuration file
       equivalent. Currently, Routinator will only start listening on these
       ports after an initial validation run has finished.

       It will not listen on any sockets unless explicitly specified. It will
       still run and periodically update the repository. This might be useful
       for use with :command:`vrps` mode with the :option:`-n` option.

       .. option:: -d, --detach

              If present, Routinator will detach from the terminal after a
              successful start.

       .. option:: --rtr=addr:port

              Specifies a local address and port to listen on for incoming RTR
              connections.

              Routinator supports both protocol version 0 defined in :rfc:`6810`
              and version 1 defined in :rfc:`8210`. However, it does not support
              router keys introduced in version 1.  IPv6 addresses must be
              enclosed in square brackets. You can provide the option multiple
              times to let Routinator listen on multiple address-port pairs.

       .. option:: --http=addr:port

              Specifies the address and port to listen on for incoming HTTP
              connections.  See `HTTP Service`_ below for more information on
              the HTTP service provided by Routinator.

       .. option:: --listen-systemd

              The RTR listening socket will be acquired from systemd via socket
              activation. Use this option together with systemds socket units to
              allow a Routinator running as a regular user to bind to the
              default RTR port 323.

              Currently,  all TCP listener sockets handed over by systemd will
              be used for the RTR protocol.

       .. option:: --refresh=seconds

              The amount of seconds the server should wait after having finished
              updating and validating the local repository before starting to
              update again. The next update will earlier if objects in the
              repository expire earlier. The default value is 600 seconds.

       .. option:: --retry=seconds

              The amount of seconds to suggest to an RTR client to wait before
              trying to request data again if that failed. The default value
              is 600 seconds, the value recommended in :rfc:`8210`.

       .. option:: --expire=seconds

              The amount of seconds to an RTR client can keep using data if it
              cannot refresh it. After that time, the client should discard the
              data.  Note that this value was introduced in version 1 of the RTR
              protocol and is thus not relevant for clients that only implement
              version 0.  The default value, as recommended in :rfc:`8210`, is
              7200 seconds.

       .. option:: --history=count

              In RTR, a client can request to only receive the changes that
              happened since the last version of the data it had seen. This
              option sets how many change sets the server will at most keep. If
              a client requests changes from an older version, it will get the
              current full set.

              Note that routers typically stay connected with their RTR server
              and therefore really only ever need one single change set.
              Additionally, if RTR server or router are restarted, they will
              have a new session with new change sets and need to exchange a
              full data set, too. Thus, increasing the value probably only ever
              increases memory consumption.

              The default value is 10.

       .. option:: --pid-file=path

              States a file which will be used in daemon mode to store the
              processes PID.  While the process is running, it will keep the
              file locked.

       .. option:: --working-dir=path

              The working directory for the daemon process. In daemon mode,
              Routinator will change to this directory while detaching from the
              terminal.

       .. option:: --chroot=path

              The root directory for the daemon process. If this option is
              provided, the daemon process will change its root directory to the
              given directory. This will only work if all other paths provided
              via the configuration or command line options are under this
              directory.

       .. option:: --user=user-name

              The name of the user to change to for the daemon process. It this
              option is provided, Routinator will run as that user after the
              listening sockets for HTTP and RTR have been created. The option
              has no effect unless :option:`--detach` is also used.

       .. option:: --group=group-name

              The name of the group to change to for the daemon process.  It
              this option is provided, Routinator will run as that group after
              the listening sockets for HTTP and RTR have been created.  The
              option has no effect unless :option:`--detach` is also used.


:command:`update`
       Updates the local repository by resyncing all known publication points.
       The command will also validate the updated repository to discover any
       new publication points that appear in the repository and fetch their
       data.

       As such, the command really is a shortcut for running
       :command:`routinator` :command:`vrps` :option:`-f none`.

       .. option:: --complete

              If any of the rsync commands needed to update the repository
              failed, complete the operation but provide exit status 2.  If this
              option is not given, the operation will complete with exit status
              0 in this case.


:command:`man`
       Displays the manual page, i.e., this page.

       .. option:: -o file, --output=file

              If this option is provided, the manual page will be written to the
              given file instead of displaying it. Use - to output the manual
              page to standard output.

Trust Anchor Locators
---------------------
RPKI uses trust anchor locators, or TALs, to identify the location and public
keys of the trusted root CA certificates. Routinator keeps these TALs in files
in the TAL directory which can be set by the  :option:`-t` option. If the
:option:`-b` option is used instead, the TAL directory will be in the
subdirectory *tals* under the directory specified in this option.  The default
location,  if no options are used at all is :file:`$HOME/.rpki-cache/tals`.

This directory can be created and populated with the TALs of the five Regional
Internet Registries (RIRs) via the :command:`init` command.

If the directory does exist,  Routinator will use all files with an extension
of *.tal* in this directory. This means that you can add and remove trust
anchors by adding and removing files in this directory. If you add files, make
sure they are in the format described by :rfc:`7730` or the upcoming
:rfc:`8630`.

.. _doc_routinator_manpage_configfile:

Configuration File
------------------
Instead of providing all options on the command line, they can also be provided
through a configuration file. Such a file can be selected through the
:option:`-c` option. If no configuration file is specified this way but a file
named :file:`$HOME/.routinator.conf` is present, this file is used.

The configuration file is a file in TOML format. In short, it consists of a
sequence of key-value pairs, each on its own line. Strings are to be enclosed in
double quotes. Lists can be given by enclosing a comma-separated list of values
in square brackets.

The configuration file can contain the following entries. All path values are
interpreted relative to the directory the configuration file is located in. All
values can be overwritten via the command line options.

repository-dir
      A string containing the path to the directory to store the local
      repository in. This entry is mandatory.

tal-dir
      A string containing the path to the directory that contains the Trust
      Anchor Locators. This entry is mandatory.

exceptions
      A list of strings, each containing the path to a file with local
      exceptions. If missing, no local exception files are used.

strict
      A boolean specifying whether strict validation should be employed. If
      missing, strict validation will not be used.

disable-rsync
      A boolean value that, if present and true, turns off the use of rsync.

rsync-command
      A string specifying the command to use for running rsync. The default is
      simply *rsync*.

rsync-args
      A list of strings containing the arguments to be passed to the rsync
      command.  Each string is an argument of its own.

      If this option is not provided, Routinator will try to find out if your
      rsync understands the ``--contimeout`` option and, if so, will set it to
      10 thus letting connection attempts time out after ten seconds. If your
      rsync is too old to support this option, no arguments are used.

rsync-timeout
      An integer value specifying the number seconds an rsync command is allowed
      to run before it is being terminated. The default if the value is missing
      is 300 seconds.

disable-rrdp
      A boolean value that, if present and true, turns off the use of RRDP.

rrdp-timeout
      An integer value that provides a timeout in seconds for all individual
      RRDP-related network operations, i.e., connects, reads, and writes. If the
      value is missing, a default timeout of 30 seconds will be used. Set the
      value to 0 to turn the timeout off.

rrdp-connect-timeout
      An integer value that, if present, sets a separate timeout in seconds for
      RRDP connect requests only.


rrdp-local-addr
      A string value that provides the local address to be used by RRDP
      connections.


rrdp-root-certs
      A list of strings each providing a path to a file containing a trust
      anchor certificate for HTTPS authentication of RRDP connections. In
      addition to the certificates provided via this option, the system's own
      trust store is used.


rrdp-proxies
      A list of string each providing the URI for a proxy for outgoing RRDP
      connections. The proxies are tried in order for each request. HTTP and
      SOCKS5 proxies are supported.


dirty
      A boolean value which, if true, specifies that unused files and
      directories should not be deleted from the repository directory after each
      validation run.  If left out, its value will be false and unused files
      will be deleted.

validation-threads
      An integer value specifying the number of threads to be used during
      validation of the repository. If this value is missing, the number of CPUs
      in the system is used.

log-level
      A string value specifying the maximum log level for which log messages
      should be emitted. The default is warn.

log
      A string specifying where to send log messages to. This can be
      one of the following values:

      default
             Log messages will be sent to standard error if Routinator
             stays attached to the terminal or to syslog if it runs in
             daemon mode.

      stderr
             Log messages will be sent to standard error.

      syslog
             Log messages will be sent to syslog.

      file
             Log messages will be sent to the file specified through
             the log-file configuration file entry.

      The default if this value is missing is, unsurprisingly, default.

log-file
      A string value containing the path to a file to which log messages will be
      appended if the log configuration value is set to file.  In this case, the
      value is mandatory.

syslog-facility
      A string value specifying the syslog facility to use for logging to
      syslog. The default value if this entry is missing is daemon.

rtr-listen
      An array of string values each providing the address and port which the
      RTR daemon should listen on in TCP mode. Address and port should be
      separated by a colon. IPv6 address should be enclosed in square braces.

http-listen
      An array of string values each providing the address and port which the
      HTTP service should listen on. Address and port should be separated by a
      colon. IPv6 address should be enclosed in square braces.

listen-systemd
      The RTR TCP listening socket will be acquired from systemd via socket
      activation. Use this option together with systemds socket units to allow a
      Routinator running as a regular user to bind to the default RTR port 323.

refresh
      An integer value specifying the number of seconds Routinator should wait
      between consecutive validation runs in server mode. The next validation
      run will happen earlier, if objects expire ealier. The default is 600
      seconds.

retry
      An integer value specifying the number of seconds an RTR client is
      requested to wait after it failed to receive a data set. The default is
      600 seconds.

expire
      An integer value specifying the number of seconds an RTR client is
      requested to use a data set if it cannot get an update before throwing it
      away and continuing with no data at all. The default is 7200 seconds if it
      cannot get an update before throwing it away and continuing with no data
      at all. The default is 7200 seconds.

history-size
      An integer value specifying how many change sets Routinator should keep in
      RTR server mode. The default is 10.

pid-file
      A string value containing a path pointing to the PID file to be used in
      daemon mode.

working-dir
      A string value containing a path to the working directory for the daemon
      process.

chroot
      A string value containing the path any daemon process should use as its
      root directory.

user
      A string value containing the user name a daemon process should run as.

group
      A string value containing the group name a daemon process should run as.


HTTP Service
------------
Routinator can provide an HTTP service allowing to fetch the Validated ROA
Payload in various formats. The service does not support HTTPS and should only
be used within the local network.

The service only supports GET requests with the following paths:


:command:`/csv`
      Returns the current set of VRPs in **csv** output format.

:command:`/json`
      Returns the current set of VRPs in **json** output format.

:command:`/metrics`
      Returns a set of monitoring metrics in the format used by
      Prometheus.

:command:`/openbgpd`
      Returns the current set of VRPs in **openbgpd** output format.

:command:`/rpsl`
      Returns the current set of VRPs in **rpsl** output format.

:command:`/status`
      Returns the current status of the Routinator instance. This is similar to
      the output of the **/metrics** endpoint but in a more human friendly
      format.

:command:`/version`
      Returns the version of the Routinator instance.

:command:`/api/v1/validity/as-number/prefix`
      Returns a JSON object describing whether the route announcement given by
      its origin AS number and address prefix is RPKI valid, invalid, or not
      found.  The returned object is compatible with that provided by the RIPE
      NCC RPKI Validator. For more information, see
      https://www.ripe.net/support/documentation/developer-documentation/rpki-validator-api

:command:`/validity?asn=as-number&prefix=prefix`
      Same as above but with a more form-friendly calling convention.

The paths that output the current set of VRPs accept filter expressions to limit
the VRPs returned in the form of a query string. The field ``filter-asn``
can be used to filter for ASNs and the field ``filter-prefix`` can be used
to filter for prefixes. The fields can be repeated multiple times.

This works in the same way as the options of the same name to the
:command:`vrps` command.


Relaxed Validation
------------------

The documents defining RPKI include a number of very strict rules regarding the
formatting of the objects published in the RPKI repository.  However, because
PRKI reuses existing technology, real-world applications produce objects that
do not follow these strict requirements.

As a consequence, a significant portion of the RPKI repository is actually
invalid if the rules are followed. We therefore introduce two validation
modes: strict and relaxed. Strict mode rejects any object that does not pass all
checks laid out by the relevant RFCs. Relaxed mode ignores a number of these
checks.

This memo documents the violations we encountered and are dealing with in
relaxed validation mode.


   Resource Certificates (:rfc:`6487`)
       Resource certificates are defined as a profile on the more general
       Internet PKI certificates defined in :rfc:`5280`.


       Subject and Issuer
              The RFC restricts the type used for CommonName attributes to
              PrintableString,  allowing only a subset of ASCII characters,
              while :rfc:`5280` allows a number of additional string types.  At
              least one CA produces resource certificates with Utf8Strings.

              In relaxed mode, we will only check that the general structure of
              the issuer and subject fields are correct and allow any number and
              types of attributes. This seems justified since RPKI explicitly
              does not use these fields.


   Signed Objects (:rfc:`6488`)
       Signed objects are defined as a profile on CMS messages defined in
       :rfc:`5652`.

       DER Encoding
              :rfc:`6488` demands all signed objects to be DER encoded while the
              more general CMS format allows any BER encoding  --  DER is a
              stricter subset of the more general BER. At least one CA does
              indeed produce BER encoded signed objects.

              In relaxed mode, we will allow BER encoding.

              Note that this isn't just nit-picking. In BER encoding,  octet
              strings can be broken up into a sequence of sub-strings. Since
              those strings are in some places used to carry encoded content
              themselves,  such an encoding does make parsing significantly more
              difficult. At least one CA does produce such broken-up strings.

Signals
-------
SIGUSR1: Reload TALs and restart validation
   When receiving SIGUSR1, Routinator will attempt to reload the TALs and, if
   that succeeds, restart validation. If loading the TALs fails, Routinator will
   exit.


Exit Status
-----------
Upon success,  the exit status 0 is returned. If any fatal error happens, the
exit status will be 1. Some commands provide a :option:`--complete` option which
will cause the exit status to be 2 if any of the rsync commands to update the
repository fail.
