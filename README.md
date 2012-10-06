pg_rep_test
===========

A tool to assist with setting up replication for testing

	Usage:
	  pg_rep_test [OPTION]
	
	Options:
	  -r REPLICAS           number of replicas to create (default: 1)
	  -p PORTS              comma-separated list of ports to use starting with
	                        primary (default: 5530,5531,5532,...)
	  -s fan|tree|chain     replication structure (default: fan)
	                        fan = all standbys connect directly to primary
	                        tree = one standby connects directly to primary, the
	                                remaining ones connect to that standby
	                        chain = no more than one standby connects to any node
	  -d DIRS               comma-separated list of directory names starting with
	                        primary (default: primary,standby1,standby2,...)
	  -a ARCHIVEDIR         create an archive directory
	  -h help               show this help then exit

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

	[Primary (5330)]
	      |
	      ------[Standby 1 (5331)]
	                  |
	                  ------[Standby 2 (5332)]
	                  |
	                  ------[Standby 3 (5333)]
	                  |
	                  ------[Standby 4 (5334)]
	                  |
	                  ------[Standby 5 (5335)]
	                  |
	                  ------[Standby 6 (5336)]
	                  |
	                  ------[Standby 7 (5337)]
	                  |
	                  ------[Standby 8 (5338)]
	                  |
	                  ------[Standby 9 (5339)]
	                  |
	                  ------[Standby 10 (5340)]
	                  |
	                  ------[Standby 11 (5341)]
	                  |
	                  ------[Standby 12 (5342)]
	                  |
	                  ------[Standby 13 (5343)]
	                  |
	                  ------[Standby 14 (5344)]
	                  |
	                  ------[Standby 15 (5345)]
	
