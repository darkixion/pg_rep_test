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
	  -T                     don't generate management tool script file to manage the
                                 new instances
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


## Replication structures

These are ASCII-diagrams of the 3 replication structures.

### Fan

	[Primary (5530)]
	    |
	    ---[Standby 1 (5531)]
	    |
	    ---[Standby 2 (5532)]
	    |
	    ---[Standby 3 (5533)]
	    |
	    ---[Standby 4 (5534)]

### Tree

	[Primary (5530)]
	    |
	    ---[Standby 1 (5531)]
	            |
	            ---[Standby 2 (5532)]
	            |
	            ---[Standby 3 (5533)]
	            |
        	    ---[Standby 4 (5534)]

### Chain

	[Primary (5530)]
	    |
	    ---[Standby 1 (5531)]
	            |
	            ---[Standby 2 (5532)]
	                    |
	                    ---[Standby 3 (5533)]
	                            |
	                            ---[Standby 4 (5534)]

## Notes

You can run pg_rep_test without any parameters which will then just create a primary
with a single standby.

If a list of ports isn't specified, it will use a list of unused ports starting from
5530.  If a port is in use, the next port will be checked and so on, until every
instance has a port it can use.

If the default directory names (i.e. primary, standby1, standby2 etc.) already exist
in the directory then new ones will be generated with a numeric suffix. (e.g.
primary_1, standby1_1 etc.)

All configuration changes that pg_rep_test makes get put into a separate
configuration file called custom.conf.  This is then referenced by postgresql.conf
using an 'include' directive at the end of the file.

A maintenance tool script is generated automatically after creating a set of
instances (unless suppressed with -T).  The following section describes its use.


tool(_n).pg_rep_test
================

A maintenance tool script to manage sets of instances created by pg_rep_test.
The name of the tool is unique to a set of instances.

	Usage:
	  ./tool.pg_rep_test [OPTION] [ACTION]

	Options:
	  -D DATADIRS            comma-separated list of directories of instances
	                         to affect
	  -m MODE                shutdown mode (smart  fast or immediate); must be
	                         used with the 'stop' action
	  -l LOGFILE             write log messages to a file
	  -?                     show this help then exit

	ACTION can be one of:
	  status                 shows a list of all managed instances  their ports
	                         and data directories
	  start                  start all or specified instances
	  stop                   stops all or specified instances  which can be used
	                         with the -m option
	  restart                restart all or specified instances
	  destroy                stops (if started) and deletes all or specified
	                         instances; also deletes this maintenance script

## Example

To stop all instances managed by the tool script immediately, you would run the
following:

	./tool.pg_rep_test -m immediate stop

To check the status of all instances managed by the script, you would run:

	./tool.pg_rep_test status

This would produce output similar to the following:

	Instance 'primary' on port [5536]: RUNNING
	Instance 'standby1' on port [5537]: RUNNING
	Instance 'standby2' on port [5538]: RUNNING

To stop and delete all instances, and the tool script itself, you would run:

	./tool.pg_rep_test destroy

## Notes

If a set of instances is created by pg_rep_test and a tool.pg_rep_test script
already exists in the same directory, the tool script will be given a distinct
name.  This is so that multiple tool scripts, each associated with a particular
set of instances, can exist alongside each other.  So after the first script is
created, subsequent ones will be named tool_1.pg_rep_test, tool_2... tool_3...
etc.

## License

pg_rep_test is licensed under The PostgreSQL License
(http://www.opensource.org/licenses/postgresql),
which is closely related to the BSD license.
