pg_rep_test
===========

A tool to assist with setting up replication for testing

	Usage:
	  ./pg_rep_test [OPTION]
	
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

To create 15 standbys, each using cascading replication, you would run the following:

`./pg_test_rep -r 15 -s chain`

This would create a directory named "primary", and 15 directories named
"standby1", "standby2"... "standby15".  Each standby would be configured
to connect to the previous standby, and the first standby would connect
to the primary.
