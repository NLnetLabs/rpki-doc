.. _doc_routinator_manpage:

Manual Page
===========

NAME
----
**routinator** - RPKI relying party software

SYNOPSIS
--------
**routinator**
[**-b**  *base-dir*]
[**-r** *repository-dir*]
[**-t** *tal-dir*]
[**-x** *exceptions-file* [**-x** *exceptions-file*  [...]]]
[**-\-\-strict**]
[**-\-\-disable-rsync**]
[**-\-\-rsync-command=** *command*]
[**-\-\-rsync-args=** *args*]
[**-\-\-rsync-timeout=** *seconds*]
[**-\-\-disable-rrdp**]
[**-\-\-rrdp-timeout=** *seconds*]
[**-\-\-rrdp-connect-timeout=** *seconds*]
[**-\-\-rrdp-local-addr=** *addr*]
[**-\-\-rrdp-root-cert=** *path* [...]]
[**-\-\-rrdp-proxy=** *uri* [...]]
[**-\-\-dirty**]
[**-\-\-validation-threads=** *count*]
[**-v** | **-vv** | **-q** | **-qq**]
[**-h**]
[**-V**]
command
[args]

**routinator** [options] **init** [**-f**]

**routinator**  [options] **vrps**  [**-o** *output-file*]
[**-f** *format*] [**-n**] [**-a** *asn*] [**-p** *prefix*]

**routinator** [options] **validate** [**-n**] [**-j**] [**-a** *asn*]
[**-p** *prefix*]

**routinator** [options] **server** [**-\-\-rtr** *addr*:*port* [...]]
[**-\-\-http** *addr*:*port* [...]] [**-\-\-listen-systemd**]
[**-\-\-refresh  seconds**] [**-\-\-retry seconds**]
[**-\-\-expire seconds**] [**-\-\-history count**]

**routinator** [options] **update**

**routinator** **man** [**-o** file]

DESCRIPTION
-----------
Routinator collects and processes Resource Public Key Infrastructure
(RPKI) data. It validates the Route Origin Attestations contained in
the data and makes them available to your BGP routing workflow.

It can either run in one-shot mode outputting a list of validated route
origins in various formats or as a server for the RPKI-to-Router (RTR)
protocol that routers often implement to access the data, or via HTTP.

These modes and additional operations can be chosen via commands. For
the available commands, see **COMMANDS** below.

OPTIONS
-------
The available options are:

-c path, --config=path
    Provides  the  path to a file containing basic configuration. If this  option  is  not  given, Routinator will try to use *$HOME/.routinator.conf*  if that exists. If that doesn't exist, either, default values for the options  as  described here are used.

    See CONFIGURATION FILE below for more information on the format and contents of the configuration file.


-b dir, --base-dir=dir
    Specifies the base directory  to  keep  status  information  in. Unless overwritten by the -r or -t options, the local repository will be kept in the sub-directory repository and the  TALs  will
    be kept in the sub-directory tals.
    
    If omitted, the base directory defaults to $HOME/.rpki-cache.
