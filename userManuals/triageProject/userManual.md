The SciKnowMine Triage Application
==================================

We present here a user manual for running and maintaining a web-based system for peforming document
triage given a corpus of PDF files. We will describe processes for installation, execution and maintenance 
of the system. 

*Note that this system is provided with no warranty or guarantee* 

Pre-Installation Requirements 
----------------

* MySQL 5.1 (http://www.mysql.com/)
** http://dev.mysql.com/downloads/mysql/5.1.html
* SwfTools (http://www.swftools.org/)
** http://wiki.swftools.org/wiki/Installation

The Server:
  - Must own a port number to process http requests from client web browsers.
  - Must be able to send http requests to http://eutils.ncbi.nlm.nih.gov (PubMed's eCitation services).
  - Must be able to login to MySql with a user defined login with privileges to create (and destroy) databases.

Installation
------------

This system is provided as a `\*.tar.gz` archive for Unix and Linux systems, 
a `\*.dmg` instalallable for Macs and an `\*.exe` installable for PCs.

All packages are available for download from http://bmkeg.s3-website-us-west-2.amazonaws.com/index.html#triage

Using the Triage System
-----------------------

Once installed, the system permits two modes of use: (A) using 
*command-line tools to administer the server, and upload/process scientific articles into corpora* and 
(B) use the *web interface to assign articles to specific corpora*. 

### Setting up the swfTools directory

The system uses the `pdf2swf` command to convert PDF files to the SWF file for displaying in 
FlexPaper (http://flexpaper.devaldi.com/). We therefore have to identify to the system where to find 
the pdf2swf executable with the following command.

```
setSwfToolsBinDirectory </path/to/pdf2swf/executable>
```

**Building the database**

```
mvn exec:java -Dexec.mainClass="edu.isi.bmkeg.lapdf.bin.BuildFtdDatabase" 
        -Dexec.args="<db_name> <mysql_login> <mysql_password>"        
``` 

**Adding Rule Files**

Note that you can only refer to the rule file by name in subsequent commands, so the rules files 
have to be uploaded first for the command line functions to work.

```
mvn exec:java -Dexec.mainClass="edu.isi.bmkeg.lapdf.bin.AddFTDRuleSet" 
        -Dexec.args="<path-to-rule-file> <dbName> <login> <password>"        
``` 

**Adding PDF Files**
 
Here is where you can iterate over large numbers of PDF files and upload them into the system. 

```
mvn exec:java -Dexec.mainClass="edu.isi.bmkeg.lapdf.bin.AddFTD" 
        -Dexec.args="<path-to-pdf-file/dir> <dbName> <login> <password> [<rule-file-name>]"        
``` 

**Run Rules on Files**
 
Here is where you can rerun rules over the PDF files in the database already.

```
mvn exec:java -Dexec.mainClass="edu.isi.bmkeg.lapdf.bin.RunRuleSetOnFTD" 
        -Dexec.args="<pdf-file-name> <rule-file-name> <dbName> <login> <password>"        
``` 

Running the web-app
-------------------

**First use Jetty to start the server**

```
mvn jetty:run-war
``` 

**Navigate in a browser to:**  `http://localhost:8080/lapdftextServer` 

Caveats
-------

* The system is based on AMF-messaging to a Java-based server which talks to an underlying MySQL database and so is a little slow in the upload / rule-file execution / download process.
* The system runs out of the box using the maven commands as shown but is still a research prototype.
