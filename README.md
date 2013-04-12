pg_rep_test
===========

A tool to assist with setting up replication in PostgreSQL 9.1+ for the purposes of testing or demonstration.

	Usage:
	  pg_rep_test [OPTION]
	
	Options:
	  -a ARCHIVEDIR          create an archive in the named directory
	  -d DIRS                comma-separated list of directory names starting with
	                         primary (default: primary,standby1,standby2,...)
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
	  -S                     configures all standbys to be synchronous
	  -?                     show this help then exit


## Example

To create 5 standbys, the first one connecting to the primary, and the rest connecting
to the first standby, you would run the following:

	./pg_rep_test -r 5 -s tree

This would create a directory named "primary", and 5 directories named
"standby1", "standby2"... "standby5".  Each standby would be configured
to connect to the first standby, and the first standby would connect
to the primary.

This would also produce the following ASCII-diagram which displays the
configuration they were created in and the port numbers used:

	[Primary (5530)]
	    |
	    ---[Standby 1 (5531)]
	            |
	            ---[Standby 2 (5532)]
	            |
	            ---[Standby 3 (5533)]
	            |
	            ---[Standby 4 (5534)]
	            |
	            ---[Standby 5 (5535)]


## Notes

You can run pg_rep_test without any parameters which will then just create a primary
with a single standby.

If a list of ports isn't specified, it will use a list of unused ports starting from
5530.  If a port is in use, the next port will be checked and so on, until every
instance has a port it can use.


## License

pg_rep_test is licensed under The PostgreSQL License
(http://www.opensource.org/licenses/postgresql),
which is closely related to the BSD license.
