# hadoop-sls
Custom charts with individual node usage.
Yarn Scheduler Load Simulator (SLS)

SLS is a stress and performance harness for the Yarn Resource Manager Scheduler
that exercises the scheduler implementation simulating the cluster size and the
applications load without having to have a cluster nor applications.

SLS runs a regular RM without RPC endpoints and uses a NodeManager and
Application Manager simulators to send and receive events simulating cluster
and application load behavior.

==== Quick Start ====

Let $HADOOP_ROOT represent the Hadoop install directory. If you build Hadoop
yourself, $HADOOP_ROOT is hadoop-dist/target/hadoop-$VERSION. The simulator 
is located at $HADOOP_ROOT/share/hadoop/tools/sls. The folder sls contains 
four directories: bin (running scripts), html (web portal to view progress),
sample-conf (some example configurations), and sample-data (an example rumen
trace).

STEP 1: Copy all configuration files (under sample-conf) to $HADOOP_ROOT/etc/hadoop.
STEP 2: Go to the $HADOOP_ROOT/share/hadoop/tools/sls directory, and run the simulator 
using the sample rumen trace (under sample-data).

bin/slsrun.sh —-input-rumen=sample-data/2jobs2min-rumen-jh.json —-output-dir=sample-output

The simulator will start to run, and you can track the running progress 
using its web portal (http://$HOST:10001/simulate, where $HOST is the place 
where you run the simulator.). All collected scheduler metrics are stored 
under the output-dir during running. This trace takes about 3 mins to finish.

For more detailed setup, you can check out the document 
(http://issues.apache.org/jira/secure/attachment/12604817/YARN-1021.pdf) 


## Build Instructions

Download Hadoop Binaries:
wget http://shinyfeather.com/hadoop/common/hadoop-2.7.2/hadoop-2.7.2.tar.gz

Untar archive
tar -xvzf  hadoop-2.7.2.tar.gz

cd  hadoop-2.7.2

export HADOOP_ROOT=`pwd`

export PATH="$PATH:$HADOOP_ROOT/bin"

cp -r $HADOOP_ROOT /share/hadoop/tools/sls/html/ .

Prepare SLS for Simulation

$HADOOP_ROOT/share/hadoop/tools/sls/bin/rumen2sls.sh --rumen-file=./share/hadoop/tools/sls/sample-data/2jobs2min-rumen-jh.json --output-dir=sls-input

Copy sample sls config files to hadoop config directory

mkdir $HADOOP_ROOT/etc/hadoop/old 

cp $HADOOP_ROOT/etc/hadoop/* $HADOOP_ROOT/etc/hadoop/old/

cp  $HADOOP_ROOT/share/hadoop/tools/sls/sample-conf/capacity-scheduler.xml  $HADOOP_ROOT/etc/hadoop/
cp  $HADOOP_ROOT/share/hadoop/tools/sls/sample-conf/fair-scheduler.xml      $HADOOP_ROOT/etc/hadoop/
cp  $HADOOP_ROOT/share/hadoop/tools/sls/sample-conf/sls-runner.xml          $HADOOP_ROOT/etc/hadoop/
cp  $HADOOP_ROOT/share/hadoop/tools/sls/sample-conf/ yarn-site.xml           $HADOOP_ROOT/etc/hadoop/


Run Simulation:
$HADOOP_ROOT/share/hadoop/tools/sls/bin/slsrun.sh --input-sls=./sls-input/sls-jobs.json --output-dir=output --nodes=./sls-input/sls-nodes.json --print-simulation

(Now you can view charts using http://127.0.0.1:10001/)


Custom SLS Code:

git clone git@gitlab.com:ram.parashar/hadoop-sls.git

Open pom.xml using Intellijidea open option. This will download all required dependencies. 

cd hadoop-sls.git

mvn clean 

mvn install -Dmaven.test.skip=true

cp target/hadoop-sls-2.7.2.jar $HADOOP_ROOT/share/hadoop/tools/lib

wget https://www.dropbox.com/s/am56v5lyd593i9i/chart.html?dl=0 /tmp/

By this time you are ready to run custom SLS code. 





