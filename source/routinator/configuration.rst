.. _doc_routinator_configuration:

Configuration 
=============

To get Routinator running for your specific environment, there is a great
number of elements you can configure. To get an overview of all available
options, please refer to the manual page, which can be accessed via:

.. code-block:: bash

   routinator man

.. Tip:: The manual page is available online on the `NLnet Labs documentation
		 site <https://www.nlnetlabs.nl/documentation/rpki/routinator/>`_.

Configuration File 
------------------

Routinator can take its configuration from a file. You can specify such a
configuration file via the ``-c`` option. If you don’t, Routinator will check
if there is a file ``$HOME/.routinator.conf`` and if it exists, use it. If it
doesn’t exist and there is no ``-c`` option, default values are used.

The configuration file is a TOML file. Its entries are named similarly to the
command line options. Details about the available entries and there meaning
can be found in the manual page.

In addition, a complete sample configuration file showing all the default
values can be found in the repository at `etc/routinator.conf
<https://github.com/NLnetLabs/routinator/blob/master/etc/routinator.conf>`_.

Local Exceptions 
----------------

In some cases, you may want to override the global RPKI data set with your own
local exceptions. For example, when a legitimate route announcement is
inadvertently flagged as *invalid* due to a misconfigured ROA, you may want to
temporarily accept it to give the operators an opportunity to resolve the
issue.

You can do this by specifying route origins that should be filtered out of the
output, as well as origins that should be added, in a file using JSON notation
according to the SLURM standard specified in `RFC 8416
<https://tools.ietf.org/html/rfc8416>`_.

A full example file is provided below. This, along with an empty one is
available in the repository at `/test/slurm
<https://github.com/NLnetLabs/routinator/tree/master/test/slurm>`_.

.. code-block:: json

	{
	  "slurmVersion": 1,
	  "validationOutputFilters": {
		"prefixFilters": [
		  {
			"prefix": "192.0.2.0/24",
			"comment": "All VRPs encompassed by prefix"
		  },
		  {
			"asn": 64496,
			"comment": "All VRPs matching ASN"
		  },
		  {
			"prefix": "198.51.100.0/24",
			"asn": 64497,
			"comment": "All VRPs encompassed by prefix, matching ASN"
		  }
		],
		"bgpsecFilters": [
		  {
			"asn": 64496,
			"comment": "All keys for ASN"
		  },
		  {
			"SKI": "Zm9v",
			"comment": "Key matching Router SKI"
		  },
		  {
			"asn": 64497,
			"SKI": "YmFy",
			"comment": "Key for ASN 64497 matching Router SKI"
		  }
		]
	  },
	  "locallyAddedAssertions": {
		"prefixAssertions": [
		  {
			"asn": 64496,
			"prefix": "198.51.100.0/24",
			"comment": "My other important route"
		  },
		  {
			"asn": 64496,
			"prefix": "2001:DB8::/32",
			"maxPrefixLength": 48,
			"comment": "My other important de-aggregated routes"
		  }
		],
		"bgpsecAssertions": [
		  {
			"asn": 64496,
			"comment" : "My known key for my important ASN",
			"SKI": "<some base64 SKI>",
			"routerPublicKey": "<some base64 public key>"
		  }
		]
	  }
	}
	
Use the ``-x`` option to refer to your file with local exceptions. Routinator 
will re-read that file on every validation run, so you can simply update the 
file whenever your exceptions change.