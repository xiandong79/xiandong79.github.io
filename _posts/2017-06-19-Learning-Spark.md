[toc]

# 六月上旬实验记录

## 2017-06-02

### 实验过程：

#### 问题1: 找不到 ec2 文件夹

>> Spark EC2 script has been fully moved to an external repository hosted by the UC Berkeley AMPLab



The issue is that Spark does no longer provide the ec2 directory as part of the official distribution. If you're used to spinning up your standalone clusters this way it is an issue.

[The solution is simple](https://stackoverflow.com/questions/38611573/how-to-launch-spark-2-0-on-ec2/38882774#38882774):

- Download the official ec2 directory(branch-2.0) as detailed in the Spark 2.0.0 documentation.
- If you just copy the dir to your Spark 2.0.0 and run the spark-ec2 executable to mimic the way things worked in Spark 1.*, you will be able to spin up your cluster as usual. But when you ssh into it you'll realize that none of the `binaries` are there anymore.
- So, once you spin up your cluster (as you normally would with the spark-ec2 you downloaded in step 1), you'll have to `rsync` your local directory containing Spark 2.0.0 into the master of your newly created cluster. Once this is done, you can spark-submit jobs as you normally do.


#### 问题2: Warning: SSH connection error. (This could be temporary.)

Cluster is now in 'ssh-ready' state. Waited 588 seconds.

Cloning spark-ec2 scripts from https://github.com/amplab/spark-ec2/tree/`branch-2.0` on master...

#### 问题3: launch spark-ec2
- Before You Start
```
export AWS_SECRET_ACCESS_KEY=YoOyVuU5O1C22nxAVTCrjaNnw91+jhp8G/Dbpopa
export AWS_ACCESS_KEY_ID=AKIAJ7KIZQXHBJT2M3GA
```

 - Launching a Cluster
```
./spark-ec2 -s 4 -t m4.large --key-pair=alluxio1 --identity-file=/Users/dong/Desktop/alluxio1.pem  --region=us-west-2 --zone=us-west-2a launch Xiandong-sparkcluster
```

After everything launches, check that the cluster scheduler is up and sees all the slaves by going to its web UI, which will be printed at the end of the script (typically http://<master-hostname>:8080).

 - Running Applications
```
./spark-ec2 -k <keypair> -i <key-file> login <cluster-name>
```

**To deploy code or data within your cluster**, you can log in and use the provided script `~/spark-ec2/copy-dir`, which, given a directory path, `RSYNCs` it to the same location on all the slaves.

Finally, if you get errors while running your application, look at the slave's logs for that application inside of the **scheduler work directory** (/root/spark/work). You can also view the status of the cluster using the web UI: http://<master-hostname>:8080.

 - Configuration
You can edit `/root/spark/conf/spark-env.sh` on each machine to set Spark configuration options, such as JVM options. 

This file needs to be copied to **every machine** to reflect the change. The easiest way to do this is to use a script we provide called `copy-dir`. First edit your `spark-env.sh` file on the master, then run `~/spark-ec2/copy-dir` `/root/spark/conf` to `RSYNC` it to all the workers. (关键 ！！ 去同步。)


  - Stop/Terminate a Cluster
```
./spark-ec2 destroy <cluster-name>

./spark-ec2 --region=<ec2-region> stop <cluster-name>

./spark-ec2 -i <key-file> --region=<ec2-region> start <cluster-name>
#restart it
```

#### 问题4: 上传自己修改后的jar 包 + deploy

- 方案1:

1.  将自己的repo 上传到 github. 
2.  修改 `ec2 directory` 里面的 `spark_ec2.py` 里面的 git-repo. 
 
- 方案2:
1. 在本地   `build` 一个jar
2. 将 jar 包通过 `ss` 的方式 上传到 `ec2-master`
3. 通过 `RSYNC` 的方式 将 jar 分发到各个`slave`。 
```
/root/spark-ec2/copy-dir 
# sync a directory across machines.
```


![](http://wx3.sinaimg.cn/mw690/006BEqu9gy1fgioh9oqiaj30oi0ibdir.jpg)

## 2017-06-03/04

### Benchmark Suite for Apache Spark

 Usecase 1. It enables `quantitative comparison` for Apache Spark system optimizations such as caching policy and memory management optimization, scheduling policy optimization. Researchers and developers can use Spark-Bench to comprehensively evaluate and compare the performance of their optimization and the vanilla Apache Spark.




### 实验过程 (Bench 未完成)

#### 问题1: 安装 maven
```
sudo wget http://repos.fedorapeople.org/repos/dchen/apache-maven/epel-apache-maven.repo -O /etc/yum.repos.d/epel-apache-maven.repo
sudo sed -i s/\$releasever/6/g /etc/yum.repos.d/epel-apache-maven.repo
sudo yum install -y apache-maven
mvn --version
```

```
mvn clean install -DskipTests
```

Instead to skip tests if you don't want them to be executed.

#### 问题2: Spark-Bench Configurations.
- git clone = download
- build
- 根据 Chen 的再修改 (/spark-bench/conf/env.sh)





#### 问题3: Connect to Amazon EC2 Remotely Using SSH

- Download the .pem file.

- In Amazon Dashboard choose "Instances" from the left side bar, and then select the instance you would like to connect to.

- Click on "Actions", then select "Connect"

- Click on "Connect with a Standalone SSH Client"

- Open up a Terminal window

- Create a directory:
```
# mkdir -p ~/.ssh
```
- Move the downloaded .pem file to the .ssh directory we just created:
```
# mv ~/Downloads/ec2private.pem ~/.ssh
```
- Change the permissions of the .pem file so only the root user can read it:
```
# chmod 400 ~/.ssh/ec2private.pem
```
- Create a config file:
```
# vim ~/.ssh/config
```
- Enter the following text into that config file:

Host *amazonaws.com
IdentityFile ~/.ssh/ec2private.pem
User ec2-user

- Save that file.
- Use the ssh command with your public DNS hostname to connect to your instance.
e.g.:
```
# ssh ec2-54-23-23-23-34.example.amazonaws.com
```

#### Java JDK 1.8替代 1.7.

1. Installe Java JDK.
Default 1.7 jdk installed on Amazon Linux. Upgrade Java to 1.8
```
# yum install java-1.8.0 java-1.7.0-openjdk-devel
# yum erase java-1.7.0-openjdk
```


2. Set the JAVA_HOME environment variable.

Some applications such as Apache Maven and Apache Ant require you to set the JAVA_HOME environment variable. On Amazon Linux/CentOS, OpenJDK installs into either /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.65-2.b17.7.amzn1.x86_64.

JAVA_HOME should point to the directory containing a bin/java executable. Edit user bashrc file and append the following line to export JAVA_HOME

```
vi ~/.bashrc
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.65-2.b17.7.amzn1.x86_64
source ~/.bashrc
```

#### 更简洁方案 Java 8 application on EC2

check Java current version

```
java -version
```
install Java 1.8

```
sudo yum install java-1.8.0
```
change the Java version
```
sudo alternatives --config java
sudo alternatives --config javac
```

Setup JAVA_HOME Variable
```
 # export JAVA_HOME=/opt/jdk1.8.0_51
```

Setup JRE_HOME Variable
```
 # export JRE_HOME=$JAVA_HOME/jre
```
Setup PATH Variable
```
 # export PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$PATH
```

#### What is .sh file in conf folder

An SH file is a script programmed for bash, a type of Unix shell (Bourne-Again SHell). It contains instructions written in the Bash language and can be executed by typing text commands within the shell's command-line interface.

#### What is pom.xml 

A Project Object Model (POM) provides all the configuration for a single project. General configuration covers the project's name, its owner and its dependencies on other projects. One can also configure individual phases of the build process, which are implemented as plugins. 

Larger projects should be divided into several modules, or sub-projects, each with its own POM.

![](https://upload.wikimedia.org/wikipedia/commons/thumb/c/cf/Maven_CoC.svg/354px-Maven_CoC.svg.png)

#### What is Maven/mvn

Maven is a build automation tool used primarily for Java projects. 

Maven addresses two aspects of building software: first, it describes how software is built, and second, it describes its dependencies. 

## 2017-06-05

### Spark in Intellij IDE 

#### Bug-1
```
java.lang.ClassNotFoundException: org.apache.spark.sql.types.DataType
```
应该倒入 sql lib

#### Bug-2

```

Exception in thread “main” java.lang.NoSuchMethodError: scala.Predef$.refArrayOps( [duplicate]

```

问题原因： scala version （还不支持 scala 2.12）

### Spark Web UI
Every SparkContext launches its own instance of Web UI which is available at http://[driver]:4040 by default.

>> All the information that is displayed in web UI is available thanks to JobProgressListener and other SparkListeners. One could say that web UI is a web layer to Spark listeners.

## 2017-06-06 (Bench build 成功) 

### Maven 编译成 1.8 

https://maven.apache.org/plugins/maven-compiler-plugin/examples/set-compiler-source-and-target.html

Setting the -source and -target of the Java Compiler
Sometimes when you may need to compile a certain project to a different version than what you are currently using. The javac can accept such command using -source and -target. The Compiler Plugin can also be configured to provide these options during compilation.

For example, if you want to use the Java 8 language features (-source 1.8) and also want the compiled classes to be compatible with JVM 1.8 (-target 1.8), you can either add the two following properties, which are the default property names for the plugin parameters:

```
<project>
  [...]
  <properties>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
  </properties>
  [...]
</project>
```

### 更新 yum install Scala 2.11.8 

Install Scala

Spark is based on Scala, so we need to install it early in the process

```
sudo wget http://www.scala-lang.org/files/archive/scala-2.11.8.tgz
cd /usr/local/src/
sudo mkdir scala
cd /home/ubuntu/
sudo tar -xvf scala-2.11.8.tgz -C /usr/local/src/scala
```

We’ll also need to tell Ubuntu where we installed Scala. To do this we update a setup file called ‘bashrc’

For editing, we’ll use an application called ‘vi’. This application is a very paired down file editor.

Open the bashrc file
`vi .bashrc`
Use the arrow keys to go to the bottom of the file
Press the {Insert} key
Type:

```
export SCALA_HOME=/usr/local/src/scala/scala-2.11.8
export PATH=$SCALA_HOME/bin:$PATH
```

Press {Esc}
Type :wq and Enter (to save and close)
Ask Ubuntu to read the new bashrc file

```
.bashrc
```

Verify that the the new Scala version is recognized. The command below should return something like Scala code runner version 2.11.8 …
```
scala -version
```

### Setup a Spark 2.0 Cluster + R in AWS
https://edgarsdatalab.com/2016/08/25/setup-a-spark-2-0-cluster-r-on-aws/

### Getting Spark, Python, and Jupyter Notebook running on Amazon EC2

https://medium.com/@josemarcialportilla/getting-spark-python-and-jupyter-notebook-running-on-amazon-ec2-dec599e1c297

### yum vs apt-get 


![Success](http://wx2.sinaimg.cn/mw1024/006BEqu9gy1fgbxvod7i8j30y60v4qkc.jpg)

# 六月中旬实验记录

## 2017-06-11 

### Configure the Environment

Add to the following lines to your ~/.bash_profile file.

```
export CUDA_ROOT=/usr/local/cuda 
export CUDA_HOME=$CUDA_ROOT 
export PATH=$PATH:$CUDA_ROOT/bin 
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$CUDA_ROOT/lib64:$CUDA_ROOT/extras/CUPTI/lib64
export TFoS_HOME=/root/TensorFlowOnSpark
export HADOOP_HOME=/root/ephemeral-hdfs
export SPARK_HOME=/root/spark
export PATH=${PATH}:${HADOOP_HOME}/bin:${SPARK_HOME}/bin
```

> Create TensorFlowOnSpark (TFoS) AMI on EC2

> https://github.com/yahoo/TensorFlowOnSpark/wiki/Create_AMI



###  Run a MapReduce job

Run a test MapReduce job using one of the examples included within the Hadoop distribution, e.g.

```
$ yarn jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-2.3.0.jar  pi 4 1000
```

The output is like:
```
Number of Maps = 4
Samples per Map = 1000 
Wrote input for Map #0
Wrote input for Map #1
Wrote input for Map #2
Wrote input for Map #3
Starting Job
......
Job Finished in 41.615 seconds
Estimated value of Pi is 3.14000000000000000000
```

### Hadoop InputFormat/OutputFormat

Install and compile Hadoop InputFormat/OutputFormat for TFRecords
```
git clone https://github.com/tensorflow/ecosystem.git
# follow build instructions to generate tensorflow-hadoop-1.0-SNAPSHOT.jar
# copy jar to HDFS for easier reference
hadoop fs -put tensorflow-hadoop-1.0-SNAPSHOT.jar
```

### Downloading the Script - spark-ec2 folder
([教程](https://sparkour.urizone.net/recipes/spark-ec2/))

If you are running Spark 2.x and need to download the script, visit the `AMPLab spark-ec2` repository. In the Branch: dropdown menu, select `branch-2.0`, then open the Clone or download dropdown menu as shown in the image below. Hovering the mouse over the Download ZIP button will show you the download URL.

```
$SPARK_HOME/ec2/spark-ec2 \
    --key-pair=my_key_pair \
    --identity-file=/opt/keys/my_key_pair.pem \
    --region=us-east-1 \
    --vpc-id=vpc-35481850 \
    --authorized-address=12.34.56.78/32 \
    --slaves=1 \
    --instance-type=m4.large \
    --spark-version=2.1.1 \
    --hadoop-major-version=yarn \
    --instance-profile-name=sparkour-cluster \
    launch sparkour-cluster
```

### export HADOOP_HOME
```
export HADOOP_HOME=/usr/local/aquarius/hadoop-2.2.0
export PATH=$HADOOP_HOME/bin:$PATH:...
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export HADOOP_OPTS="-Djava.library.path=$HADOOP_HOME/lib"
```

### Unsupported major.minor version 52.0

because the jar was compiled in jdk 1.8, but you are trying to run it using a jdk 1.7 environment.


### aclocal - install automake
```
/bin/sh: aclocal: command not found
```

Where does this come from and how do you fix it?

It's part of Automake . 

On CentOs
```
yum install automake
```
### 如何看 log - spark.eventLog.enabled 
1. 为了看log, 你需要先运行spark/sbin/start-history-server.sh 启动history server, 然后再访问端口18080来获得job的运行状态信息

2. 记得在/tmp 下建立文件夹 spark-events

3. 同时在spark/conf/spark-defaults.conf中 加入spark.eventLog.enabled

或者是 ： 

Setting spark-defualts.conf as
```
spark.eventLog.enabled           true
spark.eventLog.dir               hdfs://namenode:8021/directory

#or

spark.eventLog.enabled           true
spark.eventLog.dir               file:///some/where
```
原因是：

[Viewing After the Fact](http://spark.apache.org/docs/2.1.0/monitoring.html)

```
./sbin/start-history-server.sh
```
This creates a web interface at http://<server-url>:18080 by default, listing incomplete and completed applications and attempts.

> https://stackoverflow.com/questions/31233830/apache-spark-setting-spark-eventlog-enabled-and-spark-eventlog-dir-at-submit-or

![](http://wx2.sinaimg.cn/mw690/006BEqu9gy1fgioh9pjq0j30po08hdhn.jpg)


For Spark Job setup:
```
sparkConf.set("spark.eventLog.enabled","true")
```

Then check the `Spark History Server`

You can also put on old fashioned Java logging
```
import org.apache.log4j.{Level, Logger}    
 
val logger: Logger = Logger.getLogger("My.Example.Code.Rules")
Logger.getLogger("org.apache.spark").setLevel(Level.WARN)
Logger.getLogger("org.apache.spark.storage.BlockManager").setLevel(Level.ERROR)
logger.setLevel(Level.INFO)
```

### Configuring Logging
Spark uses `log4j` for logging. You can configure it by adding a `log4j.properties` file in the conf directory. One way to start is to copy the existing `log4j.properties.template` located there.

## 2017-06-14

拼接代码 - 做实验

### Install bash-completion on Amazon Linux
```
yum install bash-completion --enablerepo=epel
```
Go into /src first.

```
wget http://www.caliban.org/files/redhat/RPMS/noarch/bash-completion-20060301-1.noarch.rpm
rpm -ivh bash-completion-20060301-1.noarch.rpm
. /etc/bash_completion
```

## 2017-06-15 目的是 - Warm up Sparkcontext 

### 网上的warm-up范例

很类似的范例

来自 - Drizzle: Low Latency Execution for Apache Spark

In Drizzle, we introduce group scheduling, where multiple batches (or a group) of computation are scheduled at once. This helps decouple the granularity of task execution from scheduling and amortize the costs of task serialization and launch.

#### warm-up in DrizzleRunningSum.scala

https://github.com/amplab/drizzle-spark/blob/master/examples/src/main/scala/org/apache/spark/examples/DrizzleRunningSum.scala

```
    // Let all the executors join
    Thread.sleep(10000)
    // Warm up the JVM and copy the JAR out to all the machines etc.
    val execIds = sc.parallelize(0 until sc.getExecutorMemoryStatus.size,
      sc.getExecutorMemoryStatus.size).foreach { x =>
      Thread.sleep(1)
    }
```
#### SparkKMeans.scala

https://github.com/amplab/drizzle-spark/blob/master/examples/src/main/scala/org/apache/spark/examples/SparkKMeans.scala


### Parallelized Collections - sc.parallize()

Parallelized collections are created by calling SparkContext’s parallelize method on an existing collection in your driver program (a Scala Seq). The elements of the collection are copied to form a distributed dataset that can be operated on in parallel. For example, here is how to create a parallelized collection holding the numbers 1 to 5:
```
val data = Array(1, 2, 3, 4, 5)
val distData = sc.parallelize(data)
```

Once created, the distributed dataset (distData) can be operated on in parallel. For example, we might call `distData.reduce((a, b) => a + b)` to add up the elements of the array. We describe operations on distributed datasets later on.

One important parameter for parallel collections is the number of partitions to cut the dataset into. Spark will run one task for each partition of the cluster. Typically you want 2-4 partitions for each CPU in your cluster. Normally, Spark tries to set the number of partitions automatically based on your cluster. However, you can also set it manually by passing it as a second parameter to parallelize (e.g. `sc.parallelize(data, 10)`). Note: some places in the code use the term slices (a synonym for partitions) to maintain backward compatibility.

### Spark `map` Example Using Scala (学习Chen 的code)
```
// Basic map example in scala
scala> val x = sc.parallelize(List("spark", "rdd", "example",  "sample", "example"), 3)
scala> val y = x.map(x => (x, 1))
scala> y.collect
res0: Array[(String, Int)] = Array((spark,1), (rdd,1), (example,1), (sample,1), (example,1))

// rdd y can be re writen with shorter syntax in scala as 
scala> val y = x.map((_, 1))
scala> y.collect
res0: Array[(String, Int)] = Array((spark,1), (rdd,1), (example,1), (sample,1), (example,1))

// Another example of making tuple with string and it's length
scala> val y = x.map(x => (x, x.length))
scala> y.collect
res0: Array[(String, Int)] = Array((spark,5), (rdd,3), (example,7), (sample,6), (example,7))
```

### 记录时间 System.nanoTime() vs System.currentTimeMillis()

The most basic approach would be to simply record the start time and end time, and do subtraction.
```
val startTimeMillis = System.currentTimeMillis()

/* your code goes here */

val endTimeMillis = System.currentTimeMillis()
val durationSeconds = (endTimeMillis - startTimeMillis) / 1000

Method -2 

 // returns the current value of the system timer, in nanoseconds
    System.out.print("time in nanoseconds = ");
    System.out.println(System.nanoTime());

 // returns the current value of the system timer, in milliseconds
    System.out.print("time in milliseconds = ");
    System.out.println(System.currentTimeMillis());
      
      
``` 

## 2016-06-16 

### 修改 /bin/run.sh 里面的参数

- partition 32 to 8
- pagenum 里面 也要修改


### comment multi line in Vim

To comment out blocks in vim:
```
- press Esc (to leave editing or other mode)
- hit ctrl + v (visual block mode)
- use the up/down arrow keys to select lines you want (it won't highlight everything - it's OK!)
- Shift + i (capital I)
- insert the text you want, i.e. '% '
- press Esc.
```
### ForkJoinTaskSupport in Scala
```
new ForkJoinTaskSupport(environment: ForkJoinPool = ForkJoinTasks.defaultForkJoinPool)
```

目标问题： I want to run an operation in parallel which needs to have a set number of threads running.

- Task support

Parallel collections are modular in the way operations are scheduled. Each parallel collection is parametrized with a task support `object` which is responsible for scheduling and load-balancing tasks to processors.

The task support object internally keeps a reference to `a thread pool` implementation and decides how and when tasks are split into smaller tasks. 

Here is a way to change the `task support` of a parallel collection:
```
scala> import scala.collection.parallel._
import scala.collection.parallel._

scala> val pc = mutable.ParArray(1, 2, 3)
pc: scala.collection.parallel.mutable.ParArray[Int] = ParArray(1, 2, 3)

scala> pc.tasksupport = new ForkJoinTaskSupport(new scala.concurrent.forkjoin.ForkJoinPool(2))
pc.tasksupport: scala.collection.parallel.TaskSupport = scala.collection.parallel.ForkJoinTaskSupport@4a5d484a

scala> pc map { _ + 1 }
res0: scala.collection.parallel.mutable.ParArray[Int] = ParArray(2, 3, 4)
```

The above sets the parallel collection to use a fork-join pool with parallelism `level 2`. 

实战例子:
```
//I am using scala parallel collections.

val largeList = list.par.map(x => largeComputation(x)).toList
```
实战例子(可控制并行数)：
```
largerList.tasksupport = new ForkJoinTaskSupport(
  new scala.concurrent.forkjoin.ForkJoinPool(x)
)
largerList.map(x => largeComputation(x)).toList
```


To set the parallel collection to use a `thread pool executor`:

```
scala> pc.tasksupport = new ThreadPoolTaskSupport()
pc.tasksupport: scala.collection.parallel.TaskSupport = scala.collection.parallel.ThreadPoolTaskSupport@1d914a39

scala> pc map { _ + 1 }
res1: scala.collection.parallel.mutable.ParArray[Int] = ParArray(2, 3, 4)
```
When a parallel collection is serialized, the task support field is omitted from serialization. When deserializing a parallel collection, the task support field is set to the default value– the execution context task support.

###  Bash Script
In a Unix shell, if I want to combine stderr and stdout into the stdout stream for further manipulation, I can append the following on the end of my command:
```
2>&1
```

File descriptor 1 is the standard output (stdout).
File descriptor 2 is the standard error (stderr).
it will actually be interpreted as "redirect stderr to a file named 1". `&` indicates that what follows is a file descriptor and not a filename. So the construct becomes: 2>&1.

另外一个例子：
```
echo test > afile.txt
```
..redirects stdout to afile.txt. This is the same as doing..

```
echo test 1> afile.txt
```

To redirect stderr, you do..
```
echo test 2> afile.txt
```

`>&` is the syntax to redirect a stream to another file descriptor - 0 is stdin. 1 is stdout. 2 is stderr.

You can redirect stdout to stderr by doing..
```
echo test 1>&2 # or echo test >&2
```
..or vice versa:

```
echo test 2>&1
```
So, in short.. 2> redirects stderr to an (unspecified) file, appending &1 redirects stderr to stdout


- `2>&1 > output.log` outputs only to the file
- `2>&1 | tee output.log`is to be able to capture both stdout and stderr to a log file and to see it on the screen at the same time.

###  log 文件存储 + HDFS 用法

[HDFSCommands](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html)

The File System (FS) shell includes various shell-like commands that directly interact with the Hadoop Distributed File System (HDFS) as well as other file systems that Hadoop supports, such as Local FS, WebHDFS, S3 FS, and others. The FS shell is invoked by:
```
bin/hadoop fs <args>
```

All FS shell commands take `path URIs` as arguments. The URI format is scheme://authority/path. For HDFS the scheme is hdfs, and for the Local FS the scheme is file. An HDFS file or directory such as /parent/child can be specified as hdfs://namenodehost/parent/child or simply as /parent/child (given that your configuration is set to point to hdfs://namenodehost).

> Error information is sent to stderr and the output is sent to stdout.

If HDFS is being used, `hdfs` `dfs` is a synonym.

Relative paths can be used. For HDFS, the current working directory is the HDFS home directory `/user/<username>` that often has to be created manually. The HDFS home directory can also be implicitly accessed, e.g., when using the HDFS trash folder, the .Trash directory in the home directory.


- dfs + [command]

Usage: `hdfs` dfs [COMMAND [COMMAND_OPTIONS]]

Run a filesystem command on the file system supported in Hadoop. The various COMMAND_OPTIONS can be found at File System Shell Guide.

 - [COMMAND] 的一种 -rmr

Usage: `hadoop` fs -rmr [-skipTrash] URI [URI ...]

Recursive version of delete.

Note: This command is deprecated. Instead use hadoop fs -rm -r

## 2017-06-18

### SparkContext
Main entry point for Spark functionality. A SparkContext represents the connection to a Spark cluster, and can be used to create RDDs, accumulators and broadcast variables on that cluster.
Only one SparkContext may be active per JVM. You must stop() the active SparkContext before creating a new one.

#### setLocalProperty - Creating Logical Job Groups

- Set a local property that affects jobs submitted from this `thread`, such as the Spark fair scheduler pool. User-defined properties may also be set here. These properties are propagated through to worker tasks and can be accessed there via `TaskContext.getLocalProperty`

- after which all jobs submitted within the thread will be grouped, say into a pool by `FAIR` job scheduler.

```
sc.setLocalProperty("spark.scheduler.pool", "myPool")

// these two jobs (one per action) will run in the myPool pool
rdd.count
rdd.collect

sc.setLocalProperty("spark.scheduler.pool", null)

// this job will run in the default pool
rdd.count
```

更多：
```
import org.apache.spark.scheduler._


def setJobGroup(groupId: String, description: String, interruptOnCancel: Boolean = false) {
    setLocalProperty(SparkContext.SPARK_JOB_DESCRIPTION, description)
    setLocalProperty(SparkContext.SPARK_JOB_GROUP_ID, groupId)
    // Note: Specifying interruptOnCancel in setJobGroup (rather than cancelJobGroup) avoids
    // changing several public APIs and allows Spark cancellations outside of the cancelJobGroup
    // APIs to also take advantage of this property (e.g., internal job failures or canceling from
    // JobProgressTab UI) on a per-job basis.
    setLocalProperty(SparkContext.SPARK_JOB_INTERRUPT_ON_CANCEL, interruptOnCancel.toString)
  }

  /** Clear the current thread's job group ID and its description. */
  def clearJobGroup() {
    setLocalProperty(SparkContext.SPARK_JOB_DESCRIPTION, null)
    setLocalProperty(SparkContext.SPARK_JOB_GROUP_ID, null)
    setLocalProperty(SparkContext.SPARK_JOB_INTERRUPT_ON_CANCEL, null)
  }
  ```

#### Build Pools from XML Allocations File
in `conf/fairscheduler.xml.template`

After the new pool was registered, you should see the following INFO message in the logs:
```
INFO FairSchedulableBuilder: Created pool [poolName], schedulingMode: FIFO, minShare: 0, weight: 1
```

> Tip

Enable INFO logging level for org.apache.spark.scheduler.FairSchedulableBuilder logger to see what happens inside.

Add the following line to `conf/log4j.properties`:
```
log4j.logger.org.apache.spark.scheduler.FairSchedulableBuilder=INFO
```


#### getOrCreate
`public static SparkContext getOrCreate()`

This function may be used to get or instantiate a SparkContext and register it as a singleton object. 

Because we can only have one active SparkContext per JVM, this is useful when applications may wish to `share` a SparkContext.
This method allows not passing a SparkConf (useful if just retrieving).

## 2017-06-19

### error when import org.jblas._

error: object jblas is not a member of package org
```
import org.jblas.DoubleMatrix
```

There are three ways to use jblas :

1.	Download jblas and run it
2.	Add jblas dependeny in `pom.xml` file of your maven project.
```
<dependency>
<groupId>org.jblas</groupId>
<artifactId>jblas</artifactId>
<version>1.2.4</version>
</dependency>
```
Read more about maven at : https://maven.apache.org/
3. Clone repository from github and install locally.

### What is jblas 

 `jblas` is a fast linear algebra library for Java. jblas is based on BLAS and LAPACK, the de-facto industry standard for matrix computations, and uses state-of-the-art implementations like ATLAS for all its computational routines, making jBLAS very fast.

### What is an artifact in Maven/pom.xml

- Project Object Model (POM) 

- An artifact is a file, usually a JAR, that gets deployed to a Maven repository.

- A Maven build produces `one or more` artifacts, such as a compiled JAR and a "sources" JAR.

- Each artifact has a group ID (usually a reversed domain name, like com.example.foo), an artifact ID (just a name), and a version string. The three together uniquely identify the artifact.

- A project's dependencies are specified as artifacts.

In Maven terminology, the `artifact` is the resulting output of the maven build, generally a `jar` or war or other executable file. Artifacts in maven are identified by a coordinate system of `groupId`, `artifactId`, and `version`. Maven uses the groupId, artifactId, and version to identify dependencies (usually other jar files) needed to build and run your code.

## 2017-06-20

### Spark -trace analysis

https://github.com/kayousterhout/trace-analysis

#### Configuring Spark log

1. Spark configuration parameter spark.eventLog.enabled to true
2. the event log is written to a series of files in the folder /tmp/spark-events/ on master machine.
3. logs are stored in a file named EVENT_LOG_1 within the application's folder. 

#### Analyzing
```
python parse_logs.py EVENT_LOG_1 --waterfall-only
```
1.  --waterfall-only flag tells the script to just generate the visualization, and skip more complex performance analysis. 
2.  To see all available options, use the flag --help.

For each job in the `EVENT_LOG_1` file, the Python script will output a gnuplot file that, when plotted, will generate a waterfall depicting how time was spent by each of the tasks in the job. The plot files are named `[INPUT_FILENAME]_[JOB_ID]_waterfall.gp`. To plot the waterfall for job 0, for example:
```
gnuplot EVENT_LOG_1_0_waterfall.gp
```

#### Keep in mind:
- ` only` the total amount of time spent in each part of task execution and the start and end time of the task is accurate.
-  Spark does not currently include instrumentation to measure the time spent reading input data from disk or writing job output to disk (the ''Output write wait'' shown in the waterfall is time to write shuffle output to disk, which Spark does have instrumentation for)

#### FAQ 

##### (如何画多个图) tasks seem to be overlapping others.

This typically happens when you try to plot multiple gnuplot files with one command, e.g., with a command like:
```
gnuplot *.gp
```
Gnuplot will put all of the data into a single plot, rather than in separate PDFs. Try plotting each gnuplot file separately.

### 新建 16个 slave 的cluster 用来 trace analysis（未完成）


# 快捷键-总结

- `ag` 来[查找](https://github.com/ggreer/the_silver_searcher)文件夹里面的 function／code
- You can print out this RDD lineage with `toDebugString` as shown below.
- In spark-ec2 folder, you can `cat slaves` to find the IP of each.
- 在 IDE 里面倒入project 的时候，应该先看 pom.xml 里面 scala 和Spark 的版本，再 倒入 适当版本的 libs。

# 实验流程 -总结

## 不同的运行模式 
5个参数(OPTION in run.sh):多个spark mllib job竞争；
3个参数:一个spark mllib job和background faked job竞争；
1个参数: spark mllib job单独运行

OPTION (Kmeans 文件夹下 run.sh) 里面 写 "2 0 "这两个参数，等于总计3个参数。
	- 代表 一个 ml-job "2" + 一个背景流量
	- OPTION 里面 的 "2" 可以改成ml-job "1" (代表 Kmeans)，2（PR），3，4，5/0.

kmeans.scala 中 【 i%5 =  】 的来源是 args(*), 用途是选择选 1 (Kmeans)，2（PR），3(SVD++)，4(SVM-java)，
5（ConnectedComponent）6(DecisionTree-java)，7（LabelPropagation - 非常慢）
0(背景).

## 修改记录
1. spark 文件夹下面的 conf - spark.executor.memory 2433m - spark.executor.cores 1
2. spark-bench 文件夹下面的 conf 里面的 spark／hadoop HOME ／master = /
3.  spark-bench 文件夹下面的 Kmeans 文件夹下 的 bin 里面的 run.sh (替换为陈晨的)
4. spark-bench 文件夹下面的 Kmeans 文件夹下 的 conf 里面的 env.sh - NUM_Partation = X

- 看下每个应用哪个参数表达partition数目，保证和DataGen里面的partition数目保持一致就行.

## 如何看 log - spark.eventLog.enabled
1. 为了看log, 你需要先运行spark/sbin/start-history-server.sh 启动history server, 然后再访问端口18080来获得job的运行状态信息
2. 记得在/tmp 下建立文件夹 spark-events
3. 同时在spark/conf/spark-defaults.conf中 加入spark.eventLog.enabled

## spark-bench 中 java script 的处理
【SVM】 这个(一类) func 没有copy 进来，因为他是 java script.
1. 这个文件来自与spark-bench-master/SVM/src/main/java/SVMApp.java, 但是经过了修改，删除了SVMApp.java中的第一行"package ..."以及将main函数删掉初始化阶段修改为svm函数
2. 将我下面发给你的SVMApp.java放在KMeans/src/main/java/下面再编译