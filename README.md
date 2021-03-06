# corpusmaker

Clojure utilities to build training corpora for machine learning / NLP out of
public Wikipedia and DBpedia dumps.


## Project status

*Stalled*: this project is no longer active for licensing issues (cascading being a GPL dependency it will never be possible to contribute it to the ASF) and is being replaced by <https://github.com/ogrisel/pignlproc/> instead.

This project is experimental code. Features are implemented when needed.
Expects bugs and not implemented exceptions.


## Building from source

Get the latest version of leiningen to build from the sources:

1. [Download the script](http://github.com/technomancy/leiningen/raw/stable/bin/lein).
2. Place it on your path and chmod it to be executable.
3. Run: <tt>lein self-install</tt>

Then, at the root of the corpusmaker source tree:

    $ lein deps # install dependencies in lib/

    $ lein compile-java # compile a custom helper class for the Wikipedia parser

    $ lein uberjar # build a standalone jar with all depedencies

Note: when executing the resulting standalone jar you might get a security
exceptions with java complaining that the signed jar is invalid:

    java.lang.SecurityException: Invalid signature file digest for Manifest main attributes

This can be fixed by removing the DUMMY.SF file that is mistakenly included:

    % zip corpusmaker-standalone.jar -d META-INF/DUMMY.SF

Hackers can also use the following leiningen commands for development /
deployment purpose:

    $ JAVA_OPTS="-Xmx256m" lein test [TESTS] # run the tests in the TESTS namespaces, or all tests

    $ lein repl # launch a REPL with the project classpath configured

    $ lein pom # generate a pom.xml file suitable for maven deployment


## Fetching the data

You can get the latest wikipedia dumps for the english articles here (around
5.4GB compressed, 23 GB uncompressed):

  [enwiki-latest-pages-articles.xml.bz2](http://download.wikimedia.org/enwiki/latest/enwiki-latest-pages-articles.xml.bz2)

The DBPedia links and entities types datasets are available here:

  [wikipage_en.nt.bz2](http://downloads.dbpedia.org/3.4/en/wikipage_en.nt.bz2)

  [instancetype_en.nt.bz2](http://downloads.dbpedia.org/3.4/en/instancetype_en.nt.bz2)

  [longabstract_en.nt.bz2](http://downloads.dbpedia.org/3.4/en/longabstract_en.nt.bz2)

  [pagelinks_en.nt.bz2](http://downloads.dbpedia.org/3.4/en/pagelinks_.nt.bz2)

  [redirect_en.nt.bz2](http://downloads.dbpedia.org/3.4/en/redirect_en.nt.bz2)

All of those datasets are also available from the Amazon cloud as public EBS
volumes:

  [Wikipedia XML dataset EBS Volume](http://developer.amazonwebservices.com/connect/entry.jspa?externalID=2506): <tt>snap-8041f2e9</tt> (all languages - 500GB)

  [DBPedia Triples dataset EBS Volume](http://developer.amazonwebservices.com/connect/entry.jspa?externalID=2319): <tt>snap-63cf3a0a</tt> (all languages - 67GB)

It is planned to have crane based utility function to load them to HDFS
directly from the EBS volume.

## Usage

### Evaluate popularity of entities by counting incoming links

    $ java -Xmx1g -server -jar corpusmaker-standalone.jar count-incoming \
      --pagelinks-file pagelinks_en.nt \
      --redirect-file redirects_en.nt \
      --output-folder incoming-counts-out/

Next step: flow the popularity through the links graph using TunkRank
or PageRank style iterative algoritm.


### Build a lucene index of DBpedia resources

Build a fulltext (Lucene-based) index of the abstracts of DBpedia resources:

    $ java -Xmx1g -server -jar corpusmaker-standalone.jar build-index \
      --input-folder ~/data/dbpedia \
      --index-dir  ~/lucene/dbpedia-index

### Building a NER training / evaluation corpus

TODO: Explain howto extract a BIO-formatted corpus suitable for the training of
sequence labeling algorithms such as CRFs with
[Mallet](http://mallet.cs.umass.edu/) or [crfsuite](http://www.chokkan.org/software/crfsuite/).

### Building a document classification corpus

TODO: Explain howto extract bag of words / document frequency features suitable
for document classification

