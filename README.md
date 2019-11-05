# Power over Ethernet (PoE)

This repository contains the user interactive parts of the PoE
implementation.

Only the dataplane knows the mapping from the interface name as
presented in the CLI for configuration.  As such, the probing
and configuration of the PoE ports happens in the dataplane via
the configuration and the operational interface.

## Getting the status of the PoE ports

The dataplane has been augmented with a poe command.  This is used
to return the list of ports that support PoE via the status option.

	# /opt/vyatta/bin/vplsh -l -c 'poe status'
	{
	    "poe-status": [{
		    "name": "dp0p8",
		    "admin-status": false,
		    "oper-status": false
		},{
		    "name": "dp0p7",
		    "admin-status": true,
		    "oper-status": false
		}
	    ]
	    }

## Changing the status of the PoE ports

The configuration store is used to set the PoE parameters on a port.
Since these settings are in the configuration store, they will be
replayed/reset during a restart of the dataplane.

	poe [enable|disable <interface>] [priority low|high|critical]

The priority defaults to low if not specified.

## FAL plugin

This is currently the only defined way to interact with PoE hardware.
The dataplane will make queries using attributes to the plugin's ports
to determine the PoE status of those ports.  Unsupported queries should
be rejected by the plugin.  The user space utilities should attempt to
compensate when there is missing information (such as port consumption)
due to lack of hardware support.

Changes to the PoE status of a port is handled with l2 attribute updates.
While PoE is not an layer 2 feature, the FAL currently lacks a general
port attribute interface.

See include/fal_plugin.h in the dataplane for full details.

## Power Source Equipment (PSE)

A device that provides PoE may have multiple power supplies.  Since the
current hardware doesn't have a way to query the status of the PSEs,
there is no defined way to query hardware.  If this becomes necessary
in the future, the FAL should be extended again to add unit or device
attributes queries and a pse command added to the dataplane.
