[![Arsenal](https://rawgit.com/toolswatch/badges/master/arsenal/2017.svg)](http://www.toolswatch.org/2017/06/the-black-hat-arsenal-usa-2017-phenomenal-line-up-announced/)
[![SANS_DFIR_SUMMIT_2K17](https://img.shields.io/badge/SANS%20DFIR%20SUMMIT-2017-blue.svg)](https://www.sans.org/event/dfir-prague-2017/summit-agenda)
[![BSIDES_LUX_2K17](https://img.shields.io/badge/BSides%20Luxembourg-2017-blue.svg)](http://www.securitybsides.com/w/page/116774919/BSidesLuxembourg)
[![CYBERCAMP_2k17](https://img.shields.io/badge/CyberCamp-2017-blue.svg)](https://cybercamp.es/programa/agenda)
[![CONAND](https://img.shields.io/badge/CONAND-2017-blue.svg)](https://conand.ad/)
[![Arsenal](https://rawgit.com/toolswatch/badges/master/arsenal/2018.svg)](http://www.toolswatch.org/2018/01/black-hat-arsenal-asia-2018-great-lineup/)

[![License](https://img.shields.io/badge/License-BSD%203--Clause-blue.svg)](https://opensource.org/licenses/BSD-3-Clause)
![docker_supported](https://img.shields.io/badge/Docker-Supported-blue.svg)
[![Python 3](https://img.shields.io/badge/Python-3-brightgreen.svg)](https://github.com/THIBER-ORG/userline/)

![build_travis](https://travis-ci.org/THIBER-ORG/userline.svg?branch=master)
[![Dependency Status](https://gemnasium.com/badges/github.com/THIBER-ORG/userline.svg)](https://gemnasium.com/github.com/THIBER-ORG/userline)
[![Github Issues](http://githubbadges.herokuapp.com/THIBER-ORG/userline/issues.svg?style=flat-square)](https://github.com/THIBER-ORG/userline/issues)
[![Pending Pull-Requests](http://githubbadges.herokuapp.com/THIBER-ORG/userline/pulls.svg?style=flat-square)](https://github.com/THIBER-ORG/userline/pulls)

# UserLine

This tool automates the process of creating logon relations from MS Windows Security Events by showing a graphical relation among users domains, source and destination logons as well as session duration.

![](https://raw.githubusercontent.com/thiber-org/userline/master/img/graph.png)

It has the following output modes:
1. Standard output
1. CSV file
1. JSON file
1. Neo4J graph
1. Graphviz dot file
1. Timesketch

# Content
1. [Preparation](#preparation)
    1. [Building a Docker image](#building-a-docker-image)
    1. [Running from Docker](#running-from-docker)
1. [Command Line](#command-line)
1. [EVTx Analisys](#evtx-analisys)
1. [Indexing](#indexing)
1. [Using the tool](#using-the-tool)
1. [CSV Output](#csv-output)
1. [JSON Output](#json-output)
1. [Timesketch Output](#timesketch-output)
1. [Neo4J Export](#neo4j-export)
    1. [Querying Neo4J data](#querying-neo4j-data)
    1. [Removing Neo4J data](#removing-neo4j-data)
1. [Graphviz dot file output](#graphviz-dot-file-output)
    1. [Image generation from graph .dot file](#image-generation-from-graph-dot-file)
    1. [Import graph into Gephi](#import-graph-into-gephi)
1. [SQLite import](#sqlite-import)
1. [Processed events](#processed-events)

## Preparation
	git clone https://github.com/THIBER-ORG/userline.git
	cd userline/src
	sudo pip3 install -U -r ../requirements.txt

### Building a Docker image
Optionally you can build a Docker image as follows:

	git clone https://github.com/THIBER-ORG/userline.git
	cd userline
	docker build . -t userline

### Running from Docker
To work with UserLine when using the Docker image, use the following syntax:

	docker run --rm -ti --net=host -v [YOUR_DATA_PATH]:/data userline userline [PARAMETERS]

Example:

	docker run --rm -ti --net=host -v $(pwd)/data:/data userline userline -h

**Note**: ``--net=host`` is only required if you're running ElasticSearch/Neo4J in another container on the same host.

## Command line

	$ ./userline.py -h
	
	 /\ /\  ___  ___ _ __ / /(_)_ __   ___ 
	/ / \ \/ __|/ _ \ '__/ / | | '_ \ / _ \
	\ \_/ /\__ \  __/ | / /__| | | | |  __/
	 \___/ |___/\___|_| \____/_|_| |_|\___|  v0.2.4b
	
	Author: Chema Garcia (aka sch3m4)
	        @sch3m4
	        https://github.com/thiber-org/userline
	
	usage: userline.py [-h] [-H ESHOSTS] [-S POOL_SIZE] -i INDEX [-r URL]
	                   (-x | -L | -E | -l | -w DATE) [-c PATH] [-j PATH] [-n BOLT]
	                   [-g PATH] [-K PATH] [-F] [-d] [-f] [-s] [-t MIN_DATE]
	                   [-T MAX_DATE] [-p PATTERN] [-I] [-k] [-v] [-m DATETIME]
	
	optional arguments:
	  -h, --help            show this help message and exit
	
	Required arguments:
	  -H ESHOSTS, --eshosts ESHOSTS
	                        Single or comma separated list of ElasticSearch hosts
	                        to query (default: localhost)
	  -S POOL_SIZE, --pool-size POOL_SIZE
	                        Connection pool size (default: 5)
	  -i INDEX, --index INDEX
	                        Index name/pattern
	  -r URL, --redis URL   Redis URL (format: redis://:pass@host:port/db)
	
	Actions:
	  -x, --inspect         Gets some statistics about the indexed data
	  -L, --last-shutdown   Gets last shutdown data
	  -E, --last-event      Gets last event data
	  -l, --logons          Shows user logon activity
	  -w DATE, --who-was-at DATE
	                        Shows only logged on users at a given time
	
	Output:
	  -c PATH, --csv-output PATH
	                        CSV Output file
	  -j PATH, --json-output PATH
	                        JSON Output file
	  -n BOLT, --neo4j BOLT
	                        Neo4j bolt with auth (format:
	                        bolt://user:pass@host:port)
	  -g PATH, --graphviz PATH
	                        Graphviz .dot file
	  -K PATH, --timesketch PATH
	                        Timesketch CSV file
	
	CSV options:
	  -F, --disable-timeframe
	                        Do not create timeframe entries
	
	JSON options:
	  -d, --duplicate-events
	                        Duplicate events (logon & logoff)
	
	Neo4J options:
	  -f, --neo4j-full-info
	                        Saves full logon/logoff info in Neo4j relations
	
	Graph (Neo4J/Graphviz) options:
	  -s, --unique-logon-rels
	                        Sets unique logon relations
	
	Optional filtering arguments:
	  -t MIN_DATE, --min-date MIN_DATE
	                        Searches since specified date (default: 2016-07-23)
	  -T MAX_DATE, --max-date MAX_DATE
	                        Searches up to specified date (default: 2017-07-23)
	  -p PATTERN, --pattern PATTERN
	                        Includes pattern in search
	  -I, --include-local   Includes local services logons (default: Excluded)
	  -k, --include-locks   Includes workstation/screensaver lock events (default:
	                        Excluded)
	  -v, --verbose         Enables verbose mode
	
	Extra information:
	  -m DATETIME, --mark-if-logged-at DATETIME
	                        Marks logged in users at a given time

## EVTx Analisys

Analyze EVTx files with [plaso](https://github.com/log2timeline/plaso)

	$ docker run -v /mnt/IR/1329585/:/data log2timeline/plaso log2timeline --hashers md5,sha256 -z Europe/Madrid /data/processed/events/windows/security/sec-evtx.plaso /data/evidences/events/windows/security/


## Indexing

**Note**: psort elastic output is really slow, for better performance upload the .plaso file to [TimeSketch](https://github.com/google/timesketch)

If your image does not already support it, use the included Plaso Dockerfile:

	$ cd userline/plaso
	$ docker build . -t plaso/es

Process the events and store them into elasticsearch

	$ docker run -ti --net="host" -v /mnt/IR/1329585/:/data plaso/es psort.py -o elastic --server 172.21.0.2 --port 9200 --doc_type plaso --index_name ir-1329585-events-security-windows /data/processed/events/windows/security/sec-evtx.plaso


## Using the tool

Getting the last shutdown event:

	$ ./userline.py -i ir-1329585-events-security-windows --last-shutdown
	
	 /\ /\  ___  ___ _ __ / /(_)_ __   ___ 
	/ / \ \/ __|/ _ \ '__/ / | | '_ \ / _ \
	\ \_/ /\__ \  __/ | / /__| | | | |  __/
	 \___/ |___/\___|_| \____/_|_| |_|\___|  v0.2.4b
	
	Author: Chema Garcia (aka sch3m4)
	        @sch3m4
	        https://github.com/thiber-org/userline
	
	INFO - Last shutdown:
	INFO - Computer: ws01.evil.corp
	INFO - 	- Datetime: 2016-07-12 18:56:33+00:00
	INFO - 	- Uptime:   124 days, 23:24:03
	INFO - 	- EvtIndex: ir-1329585-events-security-windows
	INFO - 	- EvtId:    AVsRMBloEoASMdQErSf-

Getting the last event:

	$ ./userline.py -i ir-1329585-events-security-windows --last-event
	
	 /\ /\  ___  ___ _ __ / /(_)_ __   ___ 
	/ / \ \/ __|/ _ \ '__/ / | | '_ \ / _ \
	\ \_/ /\__ \  __/ | / /__| | | | |  __/
	 \___/ |___/\___|_| \____/_|_| |_|\___|  v0.2.4b
	
	Author: Chema Garcia (aka sch3m4)
	        @sch3m4
	        https://github.com/thiber-org/userline
	
	INFO - Last event:
	INFO - Computer: ws01.evil.corp
	INFO - {
	    "computer": "ws01.evil.corp",
	    "datetime": "2017-02-14 05:04:36+00:00",
	    "description": "N/A",
	    "domain": "N/A",
	    "eventid": 6006,
	    "id": "cbc2794961fa5ced4366ef52673479faf4df5a53ca66280263526bbe0bee13af",
	    "index": "ir-1329585-events-security-windows",
	    "ipaddress": "N/A",
	    "logonid": "N/A",
	    "raw": "<Event xmlns=\"http://schemas.microsoft.com/win/2004/08/events/event\">\n  <System>\n    <Provider Name=\"EventLog\"/>\n    <EventID Qualifiers=\"32768\">6006</EventID>\n    <Level>4</Level>\n    <Task>0</Task>\n    <Keywords>0x0080000000000000</Keywords>\n    <TimeCreated SystemTime=\"2017-02-14T05:44:36.000000000Z\"/>\n    <EventRecordID>784</EventRecordID>\n    <Channel>System</Channel>\n    <Computer>ws01.evil.corp</Computer>\n    <Security/>\n  </System>\n  <EventData>\n    <Binary>0100000000000000</Binary>\n  </EventData>\n</Event>\n",
	    "sourceid": "AOsBX5IrkRtSdYVCbxr4",
	    "srcid": "N/A",
	    "timestamp": 1492458753000,
	    "type": "N/A",
	    "username": "N/A"
	}

## CSV Output
Getting logon relations between two dates into a CSV file:

	$ ./userline.py -l -i ir-1329585-events-security-windows -t 2016-11-20T11:00:00 -T 2016-11-21T11:00:00 -c output.csv
	
	 /\ /\  ___  ___ _ __ / /(_)_ __   ___ 
	/ / \ \/ __|/ _ \ '__/ / | | '_ \ / _ \
	\ \_/ /\__ \  __/ | / /__| | | | |  __/
	 \___/ |___/\___|_| \____/_|_| |_|\___|  v0.2.4b
	
	Author: Chema Garcia (aka sch3m4)
	        @sch3m4
	        https://github.com/thiber-org/userline
	
	INFO - Building query
	INFO - Found 297 events to be processed
	INFO - Processing events
	[====================] 100.0% Elapsed: 0m 02s ETA: 0m00s
	INFO - 44 Logons processed in 0:00:02.051880

## JSON Output
Getting logon relations between two dates into a JSON file:

	$ ./userline.py -l -i ir-1329585-events-security-windows -t 2016-11-20T11:00:00 -T 2016-11-21T11:00:00 -j output.json
	
	 /\ /\  ___  ___ _ __ / /(_)_ __   ___ 
	/ / \ \/ __|/ _ \ '__/ / | | '_ \ / _ \
	\ \_/ /\__ \  __/ | / /__| | | | |  __/
	 \___/ |___/\___|_| \____/_|_| |_|\___|  v0.2.4b
	
	Author: Chema Garcia (aka sch3m4)
	        @sch3m4
	        https://github.com/thiber-org/userline
	
	INFO - Building query
	INFO - Found 297 events to be processed
	INFO - Processing events
	[====================] 100.0% Elapsed: 0m 02s ETA: 0m00s
	INFO - 44 Logons processed in 0:00:02.051880

## Timesketch Output
Getting logon relations between two dates into a Timesketch compatible CSV file:

	$ ./userline.py -l -i ir-1329585-events-security-windows -t 2016-11-20T11:00:00 -T 2016-11-21T11:00:00 -K output.csv
	
	 /\ /\  ___  ___ _ __ / /(_)_ __   ___ 
	/ / \ \/ __|/ _ \ '__/ / | | '_ \ / _ \
	\ \_/ /\__ \  __/ | / /__| | | | |  __/
	 \___/ |___/\___|_| \____/_|_| |_|\___|  v0.2.4b
	
	Author: Chema Garcia (aka sch3m4)
	        @sch3m4
	        https://github.com/thiber-org/userline
	
	INFO - Building query
	INFO - Found 297 events to be processed
	INFO - Processing events
	[====================] 100.0% Elapsed: 0m 02s ETA: 0m00s
	INFO - 44 Logons processed in 0:00:02.051880
	
## Neo4J Export

Getting logon relations into Neo4J graph:

	$ docker run -d -p 7474:7474 -p 7687:7687 -e NEO4J_AUTH=none -v $HOME/neo4j/data:/data neo4j
	$ ./userline.py -l -i ir-1329585-events-security-windows -t 2016-11-20T11:00:00 -T 2016-11-21T11:00:00 -n "bolt://localhost:7687/"
	
	 /\ /\  ___  ___ _ __ / /(_)_ __   ___ 
	/ / \ \/ __|/ _ \ '__/ / | | '_ \ / _ \
	\ \_/ /\__ \  __/ | / /__| | | | |  __/
	 \___/ |___/\___|_| \____/_|_| |_|\___|  v0.2.4b
	
	Author: Chema Garcia (aka sch3m4)
	        @sch3m4
	        https://github.com/thiber-org/userline
	
	INFO - Building query
	INFO - Found 297 events to be processed
	INFO - Processing events
	[====================] 100.0% Elapsed: 0m 02s ETA: 0m00s
	INFO - 44 Logons processed in 0:00:02.051880

### Querying Neo4J data

	MATCH(n) RETURN(n)
	
Query the results using Neo4J CQL

![](https://raw.githubusercontent.com/thiber-org/userline/master/img/result.png)

### Removing Neo4J data

	MATCH(n)-[r]-(m) DELETE n,r,m
	MATCH(n) DELETE n
	
## Graphviz dot file output

	$ ./userline.py -l -i ir-1329585-events-security-windows -t 2016-11-20T11:00:00 -T 2016-11-21T11:00:00 -g graph.dot
	
	 /\ /\  ___  ___ _ __ / /(_)_ __   ___ 
	/ / \ \/ __|/ _ \ '__/ / | | '_ \ / _ \
	\ \_/ /\__ \  __/ | / /__| | | | |  __/
	 \___/ |___/\___|_| \____/_|_| |_|\___|  v0.2.4b
	
	Author: Chema Garcia (aka sch3m4)
	        @sch3m4
	        https://github.com/thiber-org/userline
	
	INFO - Building query
	INFO - Found 297 events to be processed
	INFO - Processing events
	[====================] 100.0% Elapsed: 0m 02s ETA: 0m00s
	INFO - 44 Logons processed in 0:00:02.051880

### Image generation from graph .dot file

Once you've generated the .dot file, you can generate an image with the graph as follows:

	$ dot -Tpng graph.dot > graph.png

### Import graph into Gephi

Once you've generated the .dot file, you can import the graph into [Gephi](https://gephi.org/):

![](https://raw.githubusercontent.com/thiber-org/userline/master/img/gephi.1.png)

## SQLite Import

Once you've generated the CSV output, you can import the data into a SQLite database and query the data through SQL queries:

	$ sqlite3 logon.db
	SQLite version 3.11.0 2016-02-15 17:29:24
	Enter ".help" for usage hints.
	sqlite> .mode csv userline
	sqlite> .import output.csv userline
	sqlite> .tables
	userline
	sqlite> .q
	$ sqliteman logon.db

![](https://raw.githubusercontent.com/thiber-org/userline/master/img/sqliteman.png)

## Processed events
### Logon events
- EVENT_WORKSTATION_UNLOCKED = 4801
- EVENT_SCREENSAVER_DISMISSED = 4803
- EVENT_LOGON = 4624
- EVENT_LOGON_EXPLICIT = 4648
- EVENT_SESSION_RECONNECTED = 4778

### Logoff events
- EVENT_WORKSTATION_LOCKED = 4800
- EVENT_SCREENSAVER_INVOKED = 4802
- EVENT_SHUTDOWN = 4609
- EVENT_LOGOFF = 4634
- EVENT_SESSION_DISCONNECTED = 4779
- EVENT_LOGOFF_INITIATED = 4647
