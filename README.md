# DBpedia Virtuoso Docker Setup Guide

## Downloading DBpedia

### Manual-downloading

To setup your endpoint with custom files

- Download your favorite version of DBpedia dataset (from the [https://downloads.dbpedia.org/{release-year}/])
- I downloaded the .bz2 files from `/core/` & `/core-i18n/en/` locations to setup my english version of DBpedia. You can choose yours!
- unpack (i.e. unzip the ttl or nt files) the downloaded files. [skip the next script-downloading part]

### Script-downloading

Get the download commands from the following repository:
```sh
git clone https://github.com/AKSW/DBpedia-docker
```

Edit the Makefile, specifying the urls or the dataset version you wish to download.
Now to download the files and ontology, run
```sh 
make download
```

Then to unzip the downloaded files, run:
```sh
make unpack
```

_You can also write your custom scripts. Also note the above script has issues downloading the files from [https://downloads.dbpedia.org/{release-year}//core/] so you might want to look into this._

## Running Virtuoso docker

If you do not have docker do:
```sh
docker pull tenforce/virtuoso
```

If you already have it, skip the above step. Then, run:
```sh
docker run --name dbpedia-virtuoso -p 8890:8890 -p 1111:1111 -v /path/to/virtuoso -v /path/to/data -d tenforce/virtuoso
```


## Move the data to /dumps folder of the virtuoso

Move the unpacked .ttl files to the `/db/dumps/` folder of the virtuoso docker repository that you created before.
```sh
mv /path/to/data/ /path/to/virtuoso/db/dumps
```

If there is an access related problem, use the above command with `sudo`


## Loading DBpedia into Virtuoso

To load the data follow the following steps:
```sh 
docker exec -it dbpedia-virtuoso bash
```


You will now be inside the docker virtuoso, then run
```sh 
Isql-v -U dba -P dba
```

You will now be inside the ISQL terminal of the virtuoso docker.


You will first need to load the ontology file (can be also loaded later, I tried it myself. What is important that you load it) to the graph [http://dbpedia.org/resource/classes#]. This will allow you to browse/query through your local endpoint like the public one, i.e. [https://dbpedia.org/sparql]. Which means you can query all the dbpedia resources in the same way you query the public endpoint at:

[http://{your-cname}:{your-port}/resource/{resource-name}]

To do this, run:

```sh 
ld_add('dumps/{ontology-filename}.owl', 'http://dbpedia.org/resource/classes#');
```


Now, we can start loading the data (RDF triples) from the ttl files. To do so, run:

```sh 
ld_dir(‘dumps/’, ’*.*’, ’http://dbpedia.org/’);
```

In the above command, the first argument is the path of the `dumps/` repository, second specifies the files to be loaded (*.* - everything in dumps, .ttl - for only ttl files in dumps, etc); and last argument is the named graph where you want all your data to be loaded. _Please make sure all your .ttl files are in the `/dumps` repository!!


Then run the rdf loader to start loading the triples in to the database, run:
```sh 
rdf_loader_run();
```


## Querying the virtuoso SPARQL endpoint

The SPARQL endpoint will be exposed at the ports declared during the docker run command. If you forgot to declare them back then, you are screwed. There is no way to do so now. Start over. Game over!


If you did declare them (as in our case, cf. docker run command), goto: http://localhost:8890/sparql

And you should be able to see the SPARQL interface.


Enjoy!
