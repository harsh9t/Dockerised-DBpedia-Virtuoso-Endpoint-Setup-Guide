# DBpedia Virtuoso Docker Setup Guide

## Downloading DBpedia

- Download your favorite version of DBpedia dataset (either manually or using the next steps)
- Script-downloading
- $git clone https://github.com/AKSW/DBpedia-docker

Edit the Makefile, specifying the urls or the dataset version you wish to download
- $make download
- $make unpack

## Running Virtuoso docker

If you do not have docker do:
- $docker pull tenforce/virtuoso

If you already have it, skip the above step. Then, run:
- $docker run --name dbpedia-virtuoso -p 8890:8890 -p 1111:1111 -v /path/to/virtuoso -v /path/to/data -d tenforce/virtuoso


## Move the data to /dumps folder of the virtuoso

Move the unpacked *.ttl files to the /db/dumps/ folder of the virtuoso docker repository that you created before.
- $mv /path/to/data/ /path/to/virtuoso/db/dumps
If there is an access related problem, use sudo


## Loading DBpedia into Virtuoso

To load the data follow the following steps:
- $docker exec -it dbpedia-virtuoso bash


You will now be inside the docker virtuoso, then run
- Isql-v -U dba -P dba


You will now be inside the ISQL terminal of the virtuoso docker. Then, run
- $ld_dir(‘dumps/’, ’*.*’, ’http://dbpedia.org/’);

In the above command, the first argument is the path of the dumps/ repository, second specifies the files to be loaded (*.* - everything in dumps, *.ttl - for only ttl files in dumps, etc); and last argument is the named graph where you want all your data to be loaded.


Then run the rdf loader to start loading the triples in the database, run:
- $rdf_loader_run();

This will start loading all the triples from the files specified before in /dumps repository


## Querying the virtuoso SPARQL endpoint

The SPARQL endpoint will be exposed at the ports declared during the docker run command. If you forgot to declare them back then, you are screwed. There is no way to do so now. Start over. Game over!


If you did declare them (as in our case, cf. docker run command), goto:

http://localhost:8890/sparql

And you should be able to see the interface.


Enjoy!
