.. _doc_routinator_configuration:

Configuration
=============

There are currently two major functions of the Routinator: printing the
list of valid route origins, also known as _Validated ROA Payload_ or VRP,
and providing the service for routers to access this list via a protocol
known as RPKI-to-Router protocol or RTR.

These (and all other functions) of Routinator are accessible on the
command line via sub-commands. The commands are `vrps` and `rtrd`,
respectively.

So, to have Routinator print the list, you say

```bash
routinator vrps
```

If this is the first time you’ve
been using Routinator, it will create `$HOME/.rpki-cache`, put the
trust anchor locators of the five RIRs there, and then complain that
ARIN’s TAL is in fact not really there.

Follow the instructions provided and try again. You can also add
additional trust anchors by simple dropping their TAL file in RFC 7730
format into `$HOME/.rpki-cache/tals`.

Now Routinator will rsync the entire RPKI repository to your machine
(which will take a while during the first run), validate it and produce
a long list of AS numbers and prefixes.

Information about additional command line arguments is available via the
`-h` option or you can look at the more detailed man page via the `man`
sub-command:

```bash
routinator man
```

It is also available online on the
[NLnetLabs documentation
site](https://www.nlnetlabs.nl/documentation/rpki/routinator/).


## Feeding a Router with RPKI-RTR

Routinator supports RPKI-RTR as specified in RFC 8210 as well as the older
version from RFC 6810. It will act as an RTR server if you start it with
the `rtrd` sub-command. It will do so as a daemon and detach from your
terminal unless you provide the `-a` (for attached) option.

You can specify the address(es) to listen on via the `-l` (or `--listen`)
option. If you don’t, it will listen on `127.0.0.1:3323` by default. This
isn’t the IANA-assigned default port for the protocol, which would be 323.
But since that is a privileged port you’d need to be running Routinator as
root when otherwise there is no reason to do that. Also, note that the
default address is a localhost address for security reasons.

So, in order to run Routinator as an RTR server listening on port 3323 on
both 192.0.2.13 and 2001:0DB8::13 without detaching from the terminal, run

```bash
routinator rtrd -a -l 192.0.2.13:3323 -l [2001:0DB8::13]:3323
```

By default, the repository will be updated and re-validated every hour as
per the recommendation in the RFC. You can change this via the
`--refresh` option and specify the interval between re-validations in
seconds. That is, if you rather have Routinator validate every fifteen
minutes, the above command becomes

```bash
routinator rtrd -a -l 192.0.2.13:3323 -l [2001:0DB8::13]:3323 --refresh=900
```