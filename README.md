[![Travis Ci](https://travis-ci.org/hbz/skos-lookup.svg?branch=master)](https://travis-ci.org/hbz/skos-lookup)
# About

A webservice to lookup SKOS concepts from an elasticsearch index.
- Start service. 
- Upload a SKOS file. 
- Perform autocompletion lookup over the SKOS Vocabular
- get back an URI for a particular SKOS Concept.


# Usage

## Java 8

	echo $JAVA_HOME //check if java 8 is configured

## Download Activator

	wget http://downloads.typesafe.com/typesafe-activator/1.3.2/typesafe-activator-1.3.2-minimal.zip
	unzip typesafe-activator-1.3.2-minimal.zip
	sudo mv activator-1.3.2-minimal /opt

## Git clone

	cd /tmp
	git clone https://github.com/hbz/skos-lookup
	cd skos-lookup

## Run

	# in order to support uploading larger skos files
	export _JAVA_OPTIONS="-Xmx2g" 
	#now run in develop mode
	/opt/activator-1.3.2-minimal/activator run

## Add sample data to index
	
	curl -i -X POST -H "Content-Type: multipart/form-data" localhost:9000/tools/skos-lookup/upload -F "data=@/tmp/skos-lookup/test/resources/agrovoc_2016-07-15_lod.nt.gz" -F"index=agrovoc_test" -F"format=NTRIPLES"
	
## Perform sample query

	curl -XGET 'localhost:9000/tools/skos-lookup/autocomplete?lang=de&q=Erdnus&callback=mycallback&index=agrovoc_test'
	
Response
	
	/**/mycallback([{"label":"Erdnuss","value":"http://aims.fao.org/aos/agrovoc/c_11368"},{"label":"Erdnussöl","value":"http://aims.fao.org/aos/agrovoc/c_25477"},{"label":"Erdnussbutter","value":"http://aims.fao.org/aos/agrovoc/c_5640"},{"label":"Erdnussmehl","value":"http://aims.fao.org/aos/agrovoc/c_35679"}])
	
	
## Example UI with jQuery autocomplete

	firefox http://localhost:9000/tools/skos-lookup/example
	
![Autocomplete example](doc/images/skos-lookup-autocomplete.png?raw=true)

**Result**

	http://localhost:9000/tools/skos-lookup/search?q=http%3A%2F%2Faims.fao.org%2Faos%2Fagrovoc%2Fc_13551&lang=de&index=agrovoc

![Lookup example](doc/images/skos-lookup-hit.png?raw=true)

## Example UI with select2

    firefox http://localhost:9000/tools/skos-lookup/example

![Tagging example](doc/images/example-select2.png?raw=true)
	
## Example UI with upload form

	firefox http://localhost:9000/tools/skos-lookup/upload
	

# Install on Ubuntu

	cd /tmp/skos-lookup
	/opt/activator-1.3.2-minimal/activator dist
	cp target/universal/skos-lookup-1.0-SNAPSHOT.zip  /tmp
	cd /tmp
	unzip skos-lookup-1.0-SNAPSHOT.zip
	mv skos-lookup-1.0-SNAPSHOT /opt/skos-lookup

edit startscript

	sudo cp /tmp/skos-lookup/install/skos-lookup.tmpl /etc/init.d/skos-lookup
	sudo chmod u+x /etc/init.d/skos-lookup
	sudo editor /etc/init.d/skos-lookup

set the following vars

	JAVA_HOME=/opt/java
	HOME="/opt/skos-lookup"
	USER="user to run skos-lookup"
	GROUP="user to run skos-lookup"
	SECRET=`uuidgen` # generate a secret e.g. using uuidgen
	PORT=9000

include into system start and shutdown

	sudo update-rc.d skos-lookup defaults 99 20
	
start

	sudo service skos-lookup start

# Update
	rm -rf /tmp/skos-lookup
	cd /tmp
	git clone https://github.com/hbz/skos-lookup
	cd /tmp/skos-lookup
	/opt/activator-1.3.2-minimal/activator dist
	cp target/universal/skos-lookup-1.0-SNAPSHOT.zip  /tmp
	cd /tmp
	unzip skos-lookup-1.0-SNAPSHOT.zip
	cp /opt/skos-lookup/conf/application.conf /tmp/skos-lookup-1.0-SNAPSHOT/conf
	cp -r /opt/skos-lookup/data /tmp/skos-lookup-1.0-SNAPSHOT
	sudo service skos-lookup stop
	rm -rf /opt/skos-lookup/*
	mv /tmp/skos-lookup-1.0-SNAPSHOT/* /opt/skos-lookup/
	sudo service skos-lookup start

# Attribution

Reuses Jakobs JSKOS context: https://gbv.github.io/jskos/jskos.html#json-ld-context

Uses Jörgs elasticsearch plugin bundle: https://github.com/jprante/elasticsearch-plugin-bundle

# LICENSE

GNU AFFERO GENERAL PUBLIC LICENSE
Version 3, 19 November 2007
