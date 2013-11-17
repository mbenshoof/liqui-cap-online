liqui-cap-online
================

Sample integration between Liquibase, Capistrano, and pt-online-schema-change

Purpose
-------

This is a proof of concept that was simply meant to show the functionality.  Essentailly, this project handles the following:

* Capturing the output of Liquibase rather than running it directly
* Parsing each statement to determine the type
* Converting CREATE INDEX statements to ALTER TABLE statements
* Switching ALTER statements to use pt-online-schema-change 

The goal is to allow non-blocking deployments of database changes within a larger deployment (i.e. Capistrano) environment.

Requirements
------------

This system was built on the following test environemnt:

* Centos 6.4
* Percona Server 5.5 [website](http://www.percona.com/downloads/Percona-Server-5.5/LATEST/RPM/rhel6/x86_64)
* Capistrano (2.9.0) [website](http://www.capistranorb.com)
  * Requires Ruby (1.8.7), Rubygem (2.1.10)
* Liquibase (2.0.5) [website](http://www.liquibase.org)
  * Requires java (1.5, not JDK) and Java MySQL connector (included)
* pt-online-schema-change (2.2.5) [website](http://www.percona.com/doc/percona-toolkit/2.2/pt-online-schema-change.html)

_Note this was a local test VM and versions were just supplied by default yum installs_

Naturally, setting these up is outside the scope of this README, but one thing I will note - when setting up using standard "yum" commands, you 
will need to specify the version of Capistrano as the current version doesn't work with the default Ruby/Rubygem install:

```
yum install ruby
yum install gcc g++ make automake autoconf curl-devel openssl-devel zlib-devel httpd-devel apr-devel apr-util-devel sqlite-devel
yum install ruby-rdoc ruby-devel
yum install rubygems
gem update --system
gem install capistrano -v 2.9.0
```

Samples
-------

As part of this repo, I've included some sample changelog files that display different ALTER operations.

To use, simply create a new, empty database (mydb in this case) and then run the capfile **run_liquibase_alters** task 3 times, each 
time changing the real changelog file the master file points to:

```
<include file="samples/changelog_01.xml" relativeToChangelogFile="true"/>
```

Once you've applied the 3 changelog files, you can run the **clean_old_tables** task to drop the old tables left by pt-osc.
_Note - these tables are left by adding the --no-drop-table flag to pt-osc to help with rollbacks_

The samples directory also contains the mysqldump output of the test schema after applying the chnages and also one showing the cleanup.
 
