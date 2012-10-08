pg_rep_test
===========

A tool to assist with setting up replication for testing

	Usage:
	  pg_rep_test [OPTION]
	
	Options:
	  -a ARCHIVEDIR          name of the archive directory (default: archive)
	  -d DIRS                comma-separated list of directory names starting with
	  -l LOGFILE             write log messages to a file, but it will not log
	                         any pre-check messages
	  -L                     enable database logging in every instance
	  -p PORTS               comma-separated list of ports to use starting with
	                         primary (default: 5530,5531,5532,...)
	  -r REPLICAS            number of replicas to create (default: 1)
	  -s fan|tree|chain      replication structure (default: fan)
	                         fan = all standbys connect directly to primary
	                         tree = one standby connects directly to primary, the
	                                remaining ones connect to that standby
	                         chain = no more than one standby connects to any node
	                         primary (default: primary,standby1,standby2,...)
	  -S                     configures all standbys to be synchronous
	  -?                     show this help then exit


## Example

To create 15 standbys, the first one connecting to the primary, and the rest connecting
to the first standby, you would run the following:

`./pg_rep_test -r 15 -s tree`

This would create a directory named "primary", and 15 directories named
"standby1", "standby2"... "standby15".  Each standby would be configured
to connect to the first standby, and the first standby would connect
to the primary.

This would also produce the following ASCII-diagram which displays the
configuration they were created in and the port numbers used:

	[Primary (5530)]
	      |
	      ------[Standby 1 (5531)]
	                  |
	                  ------[Standby 2 (5532)]
	                  |
	                  ------[Standby 3 (5533)]
	                  |
	                  ------[Standby 4 (5534)]
	                  |
	                  ------[Standby 5 (5535)]
	                  |
	                  ------[Standby 6 (5536)]
	                  |
	                  ------[Standby 7 (5537)]
	                  |
	                  ------[Standby 8 (5538)]
	                  |
	                  ------[Standby 9 (5539)]
	                  |
	                  ------[Standby 10 (5540)]
	                  |
	                  ------[Standby 11 (5541)]
	                  |
	                  ------[Standby 12 (5342)]
	                  |
	                  ------[Standby 13 (5343)]
	                  |
	                  ------[Standby 14 (5344)]
	                  |
	                  ------[Standby 15 (5345)]
	
