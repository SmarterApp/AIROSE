The following instructions have been tested on Windows and Mac OS.

We will refer to the path where the source code has been downloaded as $AIROSE_HOME
henceforth.

BUILD
-------------------------------------------------------------------------------

AIROSE uses the Maven build system. To set up using SpringSource Spring Tool Suite 
(STS), just import the maven project - set $AIROSE_HOME\AIROSE\org.sbacoss.air.airose 
as the root directory and the appropriate pom.xml should automatically be identified.  

Make sure that src/main/resources has all file patterns included (none exluded).

RUNNING
-------------------------------------------------------------------------------

All instructions here apply only when running from within STS - these do not 
deal with running the code standalone.

There are some additional resources that need to be on your classpath. These include 
the LanguageTool resources. Add both $AIROSE_HOME/AIROSE/resources/LanguageTool/org/languagetool/resource
and $AIROSE_HOME/AIROSE/resources/LanguageTool/org/languagetool/rules to the classpath by navigating to
Project>Properties>Java Build Path>Add External Class Folder.

There are a few fixes that you may need to apply to $AIROSE_HOME/AIROSE/conf/trainer.properties to 
account for the path where you have downloaded AIROSE:

1) NLP Data Path: Set the value of org.air.org.essayscorer.opennlp.datapath to 
   $AIROSE_HOME/AIROSE/resources/OpenNLPData

2) WordNet Data Path: Set the value of wordnet.database.dir to 
   $AIROSE_HOME/AIROSE/resources/WordNetData/dict

3) Database URLs for HSQLDB: Set the value of org.air.org.essayscorer.db.url to 
   jdbc:hsqldb:file:$AIROSE_HOME/AIROSE/sampledata/Kaggle-ASAP-Sample-Essay6/output/db/testdb;ifexists=false;hsqldb.write_delay=false
   Similarly set org.air.org.essayscorer.db.explicitShutdownURL to 
   jdbc:hsqldb:file:$AIROSE_HOME/AIROSE/sampledata/Kaggle-ASAP-Sample-Essay6/output/db/testdb;ifexists=false;hsqldb.write_delay=false;shutdown=true;
   
The log4j.xml (at $AIROSE_HOME/AIROSE/conf/log4j.xml) file will also need to have its appender file parameter path updated, as shown in this example:
  <param name="file" value="$AIROSE_HOME/AIROSE/log/airose.log" />

There are three separate activities in which EssayScorer may be used: 1) Training
new models 2) Validating existing models 3) Scoring responses using existing 
models.

This README describes how to do tasks 1 and 2. For task 3, there are no 
instructions at this point and you will have to look into org.opentestsystem.delivery.airose.mains.ValidationScorer 
to figure out how to invoke the Scorer.scoreResponse method.

During the course of training and validation, the software writes out a bunch 
of files which give valuable debugging information on the model that has been 
generated. There is one present under $AIROSE_HOME\AIROSE\sampledata\Kaggle-ASAP-Sample-Essay6\output
and we will use that as the output directory in instruction below. You 
may use a different one if your requirements so dictate.

For the benefit of quick-start we have included data from phase one of 
The Hewlett Foundation Automated Essay Scoring competition hosted on Kaggle.com.
(http://www.kaggle.com/c/asap-aes/data). The sample data is for essay set 6.
The data has been split into two groups: training and validation. This data has been 
slightly formatted to suit our purposes. This reformatted data is available in
the folder $AIROSE_HOME/AIROSE/sampledata/Kaggle-ASAP-Sample-Essay6 as two zip 
files: training.zip and validation.zip. 

To run either training or validation, the first step is to unzip these sample data 
packages. Extracting the zip folders will create the folders 
$AIROSE_HOME/AIROSE/sampledata/Kaggle-ASAP-Sample-Essay6/training and 
$AIROSE_HOME/AIROSE/sampledata/Kaggle-ASAP-Sample-Essay6/validation respectively.

The format of this data has been discussed in a separate section below.

To run TRAINING on the sample data (again, from within STS):

1) select org.opentestsystem.delivery.airose.mains.EssayScorerTrainer as the main class.
2) change VM arguments to have "-Xmx1000M".
3) there are five parameters that need to be supplied: -d $AIROSE_HOME\AIROSE\sampledata\Kaggle-ASAP-Sample-Essay6\training -o $AIROSE_HOME\AIROSE\sampledata\Kaggle-ASAP-Sample-Essay6\output -c $AIROSE_HOME\AIROSE\conf\trainer.properties -i kaggle -m op5 -t domain1

If using only the command line, see example below (replace $AIROSE_HOME as explained above, and $MAVEN_REPO for path to your local Maven repo):

java -Xmx1000M -Dfile.encoding=UTF-8 -classpath $AIROSE_HOME/AIROSE/org.sbacoss.air.airose/target/classes:$AIROSE_HOME/AIROSE/resources/LanguageTool/org/languagetool/resource:$AIROSE_HOME/AIROSE/resources/LanguageTool/org/languagetool/rules:$MAVEN_REPO/org/hsqldb/hsqldb/2.2.9/hsqldb-2.2.9.jar:$MAVEN_REPO/log4j/log4j/1.2.17/log4j-1.2.17.jar:$MAVEN_REPO/org/apache/opennlp/opennlp-tools/1.5.3/opennlp-tools-1.5.3.jar:$MAVEN_REPO/org/apache/opennlp/opennlp-maxent/3.0.3/opennlp-maxent-3.0.3.jar:$MAVEN_REPO/net/sf/jwordnet/jwnl/1.3.3/jwnl-1.3.3.jar:$MAVEN_REPO/org/hibernate/hibernate-core/4.1.4.Final/hibernate-core-4.1.4.Final.jar:$MAVEN_REPO/antlr/antlr/2.7.7/antlr-2.7.7.jar:$MAVEN_REPO/org/jboss/logging/jboss-logging/3.1.0.GA/jboss-logging-3.1.0.GA.jar:$MAVEN_REPO/org/jboss/spec/javax/transaction/jboss-transaction-api_1.1_spec/1.0.0.Final/jboss-transaction-api_1.1_spec-1.0.0.Final.jar:$MAVEN_REPO/dom4j/dom4j/1.6.1/dom4j-1.6.1.jar:$MAVEN_REPO/org/javassist/javassist/3.15.0-GA/javassist-3.15.0-GA.jar:$MAVEN_REPO/org/hibernate/common/hibernate-commons-annotations/4.0.1.Final/hibernate-commons-annotations-4.0.1.Final.jar:$MAVEN_REPO/org/hibernate/javax/persistence/hibernate-jpa-2.0-api/1.0.1.Final/hibernate-jpa-2.0-api-1.0.1.Final.jar:$MAVEN_REPO/org/apache/commons/commons-math3/3.2/commons-math3-3.2.jar:$MAVEN_REPO/org/languagetool/languagetool/2.0.1/languagetool-2.0.1.jar:$MAVEN_REPO/com/google/code/cjftransform/1.0.1/cjftransform-1.0.1.jar:$MAVEN_REPO/commons-logging/commons-logging/1.1.1/commons-logging-1.1.1.jar:$MAVEN_REPO/commons-validator/commons-validator/1.3.1/commons-validator-1.3.1.jar:$MAVEN_REPO/commons-beanutils/commons-beanutils/1.7.0/commons-beanutils-1.7.0.jar:$MAVEN_REPO/commons-digester/commons-digester/1.6/commons-digester-1.6.jar:$MAVEN_REPO/commons-collections/commons-collections/2.1/commons-collections-2.1.jar:$MAVEN_REPO/xml-apis/xml-apis/1.0.b2/xml-apis-1.0.b2.jar:$MAVEN_REPO/com/googlecode/ictclas4j/ictclas4j/1.0.1/ictclas4j-1.0.1.jar:$MAVEN_REPO/net/java/dev/jna/jna/3.4.0/jna-3.4.0.jar:$MAVEN_REPO/de/abelssoft/jwordsplitter/3.4/jwordsplitter-3.4.jar:$MAVEN_REPO/org/carrot2/morfologik-fsa/1.5.4/morfologik-fsa-1.5.4.jar:$MAVEN_REPO/com/carrotsearch/hppc/0.4.1/hppc-0.4.1.jar:$MAVEN_REPO/org/carrot2/morfologik-speller/1.5.4/morfologik-speller-1.5.4.jar:$MAVEN_REPO/org/carrot2/morfologik-stemming/1.5.4/morfologik-stemming-1.5.4.jar:$MAVEN_REPO/net/sourceforge/segment/segment/1.4.1/segment-1.4.1.jar:$MAVEN_REPO/org/apache/tika/tika-core/0.9/tika-core-0.9.jar:$MAVEN_REPO/com/google/code/lucene-gosen-ipadic/1.2.1/lucene-gosen-ipadic-1.2.1.jar:$MAVEN_REPO/org/apache/lucene/lucene-core/3.4.0/lucene-core-3.4.0.jar:$MAVEN_REPO/org/apache/solr/solr-core/3.4.0/solr-core-3.4.0.jar:$MAVEN_REPO/org/apache/solr/solr-noggit/3.4.0/solr-noggit-3.4.0.jar:$MAVEN_REPO/org/apache/lucene/lucene-analyzers/3.4.0/lucene-analyzers-3.4.0.jar:$MAVEN_REPO/org/apache/lucene/lucene-highlighter/3.4.0/lucene-highlighter-3.4.0.jar:$MAVEN_REPO/org/apache/lucene/lucene-memory/3.4.0/lucene-memory-3.4.0.jar:$MAVEN_REPO/org/apache/lucene/lucene-misc/3.4.0/lucene-misc-3.4.0.jar:$MAVEN_REPO/org/apache/lucene/lucene-queries/3.4.0/lucene-queries-3.4.0.jar:$MAVEN_REPO/jakarta-regexp/jakarta-regexp/1.4/jakarta-regexp-1.4.jar:$MAVEN_REPO/org/apache/lucene/lucene-spatial/3.4.0/lucene-spatial-3.4.0.jar:$MAVEN_REPO/org/apache/lucene/lucene-spellchecker/3.4.0/lucene-spellchecker-3.4.0.jar:$MAVEN_REPO/org/apache/lucene/lucene-grouping/3.4.0/lucene-grouping-3.4.0.jar:$MAVEN_REPO/org/apache/solr/solr-commons-csv/3.4.0/solr-commons-csv-3.4.0.jar:$MAVEN_REPO/org/apache/geronimo/specs/geronimo-stax-api_1.0_spec/1.0.1/geronimo-stax-api_1.0_spec-1.0.1.jar:$MAVEN_REPO/commons-codec/commons-codec/1.4/commons-codec-1.4.jar:$MAVEN_REPO/commons-fileupload/commons-fileupload/1.2.1/commons-fileupload-1.2.1.jar:$MAVEN_REPO/commons-httpclient/commons-httpclient/3.1/commons-httpclient-3.1.jar:$MAVEN_REPO/commons-io/commons-io/1.4/commons-io-1.4.jar:$MAVEN_REPO/org/apache/velocity/velocity/1.6.4/velocity-1.6.4.jar:$MAVEN_REPO/oro/oro/2.0.8/oro-2.0.8.jar:$MAVEN_REPO/org/apache/velocity/velocity-tools/2.0/velocity-tools-2.0.jar:$MAVEN_REPO/commons-chain/commons-chain/1.1/commons-chain-1.1.jar:$MAVEN_REPO/sslext/sslext/1.2-0/sslext-1.2-0.jar:$MAVEN_REPO/org/apache/struts/struts-core/1.3.8/struts-core-1.3.8.jar:$MAVEN_REPO/org/apache/struts/struts-taglib/1.3.8/struts-taglib-1.3.8.jar:$MAVEN_REPO/org/apache/struts/struts-tiles/1.3.8/struts-tiles-1.3.8.jar:$MAVEN_REPO/org/slf4j/slf4j-api/1.6.1/slf4j-api-1.6.1.jar:$MAVEN_REPO/org/slf4j/slf4j-jdk14/1.6.1/slf4j-jdk14-1.6.1.jar:$MAVEN_REPO/org/apache/solr/solr-solrj/3.4.0/solr-solrj-3.4.0.jar:$MAVEN_REPO/org/apache/zookeeper/zookeeper/3.3.1/zookeeper-3.3.1.jar:$MAVEN_REPO/jline/jline/0.9.94/jline-0.9.94.jar:$MAVEN_REPO/junit/junit/3.8.1/junit-3.8.1.jar:$MAVEN_REPO/org/codehaus/woodstox/wstx-asl/3.2.7/wstx-asl-3.2.7.jar:$MAVEN_REPO/stax/stax-api/1.0.1/stax-api-1.0.1.jar:$MAVEN_REPO/edu/mit/jwi/2.2.3/jwi-2.2.3.jar:$MAVEN_REPO/net/sf/opencsv/opencsv/2.3/opencsv-2.3.jar:$MAVEN_REPO/commons-cli/commons-cli/20040117.000000/commons-cli-20040117.000000.jar:$MAVEN_REPO/commons-lang/commons-lang/2.4/commons-lang-2.4.jar:$MAVEN_REPO/edu/ucla/sspace/sspace/2.0.3/sspace-2.0.3.jar:$MAVEN_REPO/net/sf/trove4j/trove4j/3.0.2/trove4j-3.0.2.jar:$MAVEN_REPO/org/springframework/spring-context/3.1.1.RELEASE/spring-context-3.1.1.RELEASE.jar:$MAVEN_REPO/org/springframework/spring-aop/3.1.1.RELEASE/spring-aop-3.1.1.RELEASE.jar:$MAVEN_REPO/aopalliance/aopalliance/1.0/aopalliance-1.0.jar:$MAVEN_REPO/org/springframework/spring-beans/3.1.1.RELEASE/spring-beans-3.1.1.RELEASE.jar:$MAVEN_REPO/org/springframework/spring-core/3.1.1.RELEASE/spring-core-3.1.1.RELEASE.jar:$MAVEN_REPO/org/springframework/spring-expression/3.1.1.RELEASE/spring-expression-3.1.1.RELEASE.jar:$MAVEN_REPO/org/springframework/spring-asm/3.1.1.RELEASE/spring-asm-3.1.1.RELEASE.jar org.opentestsystem.airose.mains.EssayScorerTrainer -d $AIROSE_HOME/AIROSE/sampledata/Kaggle-ASAP-Sample-Essay6/training -o $AIROSE_HOME/AIROSE/sampledata/Kaggle-ASAP-Sample-Essay6/output -c $AIROSE_HOME/AIROSE/conf/trainer.properties -i kaggle -m op5 -t domain1


To run VALIDATION on the model generated using the validation set:

1) select org.opentestsystem.delivery.airose.mains.ValidationScorer as the main class.
2) change VM arguments to have "-Xmx1000M".
3) as in the training run above, we have to provide five parameters: -d $AIROSE_HOME\AIROSE\sampledata\Kaggle-ASAP-Sample-Essay6\validation -o $AIROSE_HOME\AIROSE\sampledata\Kaggle-ASAP-Sample-Essay6\output -c $AIROSE_HOME\AIROSE\conf\trainer.properties -i kaggle -m op5 -t domain1

If using only the command line, see example below (replace $AIROSE_HOME as explained above, and $MAVEN_REPO for path to your local Maven repo):

java -Xmx1000M -Dfile.encoding=UTF-8 -classpath $AIROSE_HOME/AIROSE/org.sbacoss.air.airose/target/classes:$AIROSE_HOME/AIROSE/resources/LanguageTool/org/languagetool/resource:$AIROSE_HOME/AIROSE/resources/LanguageTool/org/languagetool/rules:$MAVEN_REPO/org/hsqldb/hsqldb/2.2.9/hsqldb-2.2.9.jar:$MAVEN_REPO/log4j/log4j/1.2.17/log4j-1.2.17.jar:$MAVEN_REPO/org/apache/opennlp/opennlp-tools/1.5.3/opennlp-tools-1.5.3.jar:$MAVEN_REPO/org/apache/opennlp/opennlp-maxent/3.0.3/opennlp-maxent-3.0.3.jar:$MAVEN_REPO/net/sf/jwordnet/jwnl/1.3.3/jwnl-1.3.3.jar:$MAVEN_REPO/org/hibernate/hibernate-core/4.1.4.Final/hibernate-core-4.1.4.Final.jar:$MAVEN_REPO/antlr/antlr/2.7.7/antlr-2.7.7.jar:$MAVEN_REPO/org/jboss/logging/jboss-logging/3.1.0.GA/jboss-logging-3.1.0.GA.jar:$MAVEN_REPO/org/jboss/spec/javax/transaction/jboss-transaction-api_1.1_spec/1.0.0.Final/jboss-transaction-api_1.1_spec-1.0.0.Final.jar:$MAVEN_REPO/dom4j/dom4j/1.6.1/dom4j-1.6.1.jar:$MAVEN_REPO/org/javassist/javassist/3.15.0-GA/javassist-3.15.0-GA.jar:$MAVEN_REPO/org/hibernate/common/hibernate-commons-annotations/4.0.1.Final/hibernate-commons-annotations-4.0.1.Final.jar:$MAVEN_REPO/org/hibernate/javax/persistence/hibernate-jpa-2.0-api/1.0.1.Final/hibernate-jpa-2.0-api-1.0.1.Final.jar:$MAVEN_REPO/org/apache/commons/commons-math3/3.2/commons-math3-3.2.jar:$MAVEN_REPO/org/languagetool/languagetool/2.0.1/languagetool-2.0.1.jar:$MAVEN_REPO/com/google/code/cjftransform/1.0.1/cjftransform-1.0.1.jar:$MAVEN_REPO/commons-logging/commons-logging/1.1.1/commons-logging-1.1.1.jar:$MAVEN_REPO/commons-validator/commons-validator/1.3.1/commons-validator-1.3.1.jar:$MAVEN_REPO/commons-beanutils/commons-beanutils/1.7.0/commons-beanutils-1.7.0.jar:$MAVEN_REPO/commons-digester/commons-digester/1.6/commons-digester-1.6.jar:$MAVEN_REPO/commons-collections/commons-collections/2.1/commons-collections-2.1.jar:$MAVEN_REPO/xml-apis/xml-apis/1.0.b2/xml-apis-1.0.b2.jar:$MAVEN_REPO/com/googlecode/ictclas4j/ictclas4j/1.0.1/ictclas4j-1.0.1.jar:$MAVEN_REPO/net/java/dev/jna/jna/3.4.0/jna-3.4.0.jar:$MAVEN_REPO/de/abelssoft/jwordsplitter/3.4/jwordsplitter-3.4.jar:$MAVEN_REPO/org/carrot2/morfologik-fsa/1.5.4/morfologik-fsa-1.5.4.jar:$MAVEN_REPO/com/carrotsearch/hppc/0.4.1/hppc-0.4.1.jar:$MAVEN_REPO/org/carrot2/morfologik-speller/1.5.4/morfologik-speller-1.5.4.jar:$MAVEN_REPO/org/carrot2/morfologik-stemming/1.5.4/morfologik-stemming-1.5.4.jar:$MAVEN_REPO/net/sourceforge/segment/segment/1.4.1/segment-1.4.1.jar:$MAVEN_REPO/org/apache/tika/tika-core/0.9/tika-core-0.9.jar:$MAVEN_REPO/com/google/code/lucene-gosen-ipadic/1.2.1/lucene-gosen-ipadic-1.2.1.jar:$MAVEN_REPO/org/apache/lucene/lucene-core/3.4.0/lucene-core-3.4.0.jar:$MAVEN_REPO/org/apache/solr/solr-core/3.4.0/solr-core-3.4.0.jar:$MAVEN_REPO/org/apache/solr/solr-noggit/3.4.0/solr-noggit-3.4.0.jar:$MAVEN_REPO/org/apache/lucene/lucene-analyzers/3.4.0/lucene-analyzers-3.4.0.jar:$MAVEN_REPO/org/apache/lucene/lucene-highlighter/3.4.0/lucene-highlighter-3.4.0.jar:$MAVEN_REPO/org/apache/lucene/lucene-memory/3.4.0/lucene-memory-3.4.0.jar:$MAVEN_REPO/org/apache/lucene/lucene-misc/3.4.0/lucene-misc-3.4.0.jar:$MAVEN_REPO/org/apache/lucene/lucene-queries/3.4.0/lucene-queries-3.4.0.jar:$MAVEN_REPO/jakarta-regexp/jakarta-regexp/1.4/jakarta-regexp-1.4.jar:$MAVEN_REPO/org/apache/lucene/lucene-spatial/3.4.0/lucene-spatial-3.4.0.jar:$MAVEN_REPO/org/apache/lucene/lucene-spellchecker/3.4.0/lucene-spellchecker-3.4.0.jar:$MAVEN_REPO/org/apache/lucene/lucene-grouping/3.4.0/lucene-grouping-3.4.0.jar:$MAVEN_REPO/org/apache/solr/solr-commons-csv/3.4.0/solr-commons-csv-3.4.0.jar:$MAVEN_REPO/org/apache/geronimo/specs/geronimo-stax-api_1.0_spec/1.0.1/geronimo-stax-api_1.0_spec-1.0.1.jar:$MAVEN_REPO/commons-codec/commons-codec/1.4/commons-codec-1.4.jar:$MAVEN_REPO/commons-fileupload/commons-fileupload/1.2.1/commons-fileupload-1.2.1.jar:$MAVEN_REPO/commons-httpclient/commons-httpclient/3.1/commons-httpclient-3.1.jar:$MAVEN_REPO/commons-io/commons-io/1.4/commons-io-1.4.jar:$MAVEN_REPO/org/apache/velocity/velocity/1.6.4/velocity-1.6.4.jar:$MAVEN_REPO/oro/oro/2.0.8/oro-2.0.8.jar:$MAVEN_REPO/org/apache/velocity/velocity-tools/2.0/velocity-tools-2.0.jar:$MAVEN_REPO/commons-chain/commons-chain/1.1/commons-chain-1.1.jar:$MAVEN_REPO/sslext/sslext/1.2-0/sslext-1.2-0.jar:$MAVEN_REPO/org/apache/struts/struts-core/1.3.8/struts-core-1.3.8.jar:$MAVEN_REPO/org/apache/struts/struts-taglib/1.3.8/struts-taglib-1.3.8.jar:$MAVEN_REPO/org/apache/struts/struts-tiles/1.3.8/struts-tiles-1.3.8.jar:$MAVEN_REPO/org/slf4j/slf4j-api/1.6.1/slf4j-api-1.6.1.jar:$MAVEN_REPO/org/slf4j/slf4j-jdk14/1.6.1/slf4j-jdk14-1.6.1.jar:$MAVEN_REPO/org/apache/solr/solr-solrj/3.4.0/solr-solrj-3.4.0.jar:$MAVEN_REPO/org/apache/zookeeper/zookeeper/3.3.1/zookeeper-3.3.1.jar:$MAVEN_REPO/jline/jline/0.9.94/jline-0.9.94.jar:$MAVEN_REPO/junit/junit/3.8.1/junit-3.8.1.jar:$MAVEN_REPO/org/codehaus/woodstox/wstx-asl/3.2.7/wstx-asl-3.2.7.jar:$MAVEN_REPO/stax/stax-api/1.0.1/stax-api-1.0.1.jar:$MAVEN_REPO/edu/mit/jwi/2.2.3/jwi-2.2.3.jar:$MAVEN_REPO/net/sf/opencsv/opencsv/2.3/opencsv-2.3.jar:$MAVEN_REPO/commons-cli/commons-cli/20040117.000000/commons-cli-20040117.000000.jar:$MAVEN_REPO/commons-lang/commons-lang/2.4/commons-lang-2.4.jar:$MAVEN_REPO/edu/ucla/sspace/sspace/2.0.3/sspace-2.0.3.jar:$MAVEN_REPO/net/sf/trove4j/trove4j/3.0.2/trove4j-3.0.2.jar:$MAVEN_REPO/org/springframework/spring-context/3.1.1.RELEASE/spring-context-3.1.1.RELEASE.jar:$MAVEN_REPO/org/springframework/spring-aop/3.1.1.RELEASE/spring-aop-3.1.1.RELEASE.jar:$MAVEN_REPO/aopalliance/aopalliance/1.0/aopalliance-1.0.jar:$MAVEN_REPO/org/springframework/spring-beans/3.1.1.RELEASE/spring-beans-3.1.1.RELEASE.jar:$MAVEN_REPO/org/springframework/spring-core/3.1.1.RELEASE/spring-core-3.1.1.RELEASE.jar:$MAVEN_REPO/org/springframework/spring-expression/3.1.1.RELEASE/spring-expression-3.1.1.RELEASE.jar:$MAVEN_REPO/org/springframework/spring-asm/3.1.1.RELEASE/spring-asm-3.1.1.RELEASE.jar org.opentestsystem.airose.mains.ValidationScorer -d $AIROSE_HOME/AIROSE/sampledata/Kaggle-ASAP-Sample-Essay6/validation -o $AIROSE_HOME/AIROSE/sampledata/Kaggle-ASAP-Sample-Essay6/output -c $AIROSE_HOME/AIROSE/conf/trainer.properties -i kaggle -m op5 -t domain1

Here is a brief description of the parameters that were supplied in step 3 
of both training and validation run configuration:
	
-d: Input Directory.  
-o: Output Directory. This is simply a scratch folder.
-c: Config File. The path of trainer.properties file.
-i: Essay Id. A unique id for the item for which we are generating models.
-m: Model Id. A group id for the model generated.
-t: Score Type. There is a possibility that we may be trying to train/predict multiple scores for different dimensions.
    This parameter uniquely identifies the dimension.



DATA FORMAT
-------------------------------------------------------------------------------

The current data format is a very cumbersome one which may be simplified in the 
future but here are the key points.

Each set - validation or training - is a folder containing CSV files only. 
Each file can contain only one line. In the sample data provided each file has 
the format 

"id","score 1", "score 2", "domain score", "response".

id: a unique identifier for the response. Unless you intend to track the processing 
    this can just be any string.
    
score 1 / score 2 / domain score: an integer valued score.

response: the actual essay we are evaluating.


All these files are processed by org.opentestsystem.airose.docprocessors.ScoreTokenizerDocProcessor.

Any change in the format of the CSV files will have to be incorporated in there.
In the code provided we do modelling on "domain score". 
