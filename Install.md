# Install

```dos
vi /etc/hostname
reboot
vi /etc/hosts
ssh-keygen
scp .ssh/id_rsa.pub master:.ssh/
cat id_rsa.pub >> authorized_keys
scp .ssh/id_rsa.pub slave:.ssh/
vi /etc/profile
```

```sh
export HADOOP_HOME=/opt/hadoop
export PATH=$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$PATH
```

```dos
source /etc/profile
vi hadoop-env.sh
```

```sh
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
```

```dos
vi core-site.xml
```

```xml
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://master:9000</value>
    </property>
    <property>
        <name>hadoop.tmp.dir</name>
        <value>hadoop/tmp</value>
    </property>
```

```dos
cp mapred-site.xml.template mapred-site.xml
vi mapred-site.xml
```

```xml
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
```

```dos
vi yarn-site.xml
```

```xml
    <property>
        <name>yarn.resourcemanager.hostname</name>
        <value>master</value>
    </property>
    <property>
      <name>yarn.nodemanager.aux-service</name>
      <value>mapreduce_shuffle</value>
    </property>
```

```dos
vi slaves
```

```text
master
slave1
slave2
```

```dos
vi hive/conf/hive-site.xml
```

```xml
<configuration>
        <property>
                <name>javax.jdo.option.ConnectionURL</name>
                <value>jdbc:mysql://localhost:3306/hive?createDatabaseIfNotExist=true</value>
        </property>
        <property>
                <name>javax.jdo.option.ConnectionDriverName</name>
                <value>com.mysql.jdbc.Driver</value>
        </property>
        <property>
                <name>javax.jdo.option.ConnectionUserName</name>
                <value>root</value>
        </property>
        <property>
                <name>javax.jdo.option.ConnectionPassword</name>
                <value>123456</value>
        </property>
</configuration>
```

```dos
cp mysql-connector-java-5.1.49.jar hive/lib/
rm -f hadoop/share/hadoop/yarn/lib/jline-0.9.94.jar
mysql -uroot -p
```

```sql
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '123456';
```

```dos
cp spark-env.sh.template spark-env.sh
vi spark-env.sh
```

```sh
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
export HADOOP_HOME=hadoop
export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
SPARK_MASTER_HOST=master
```

```dos
vi slaves
```

```text
slave1
slave2
```

```sql
-- 创建film表，分为电影名称、上映日期、票房三个字段，数据格式以“，”分割
create table film(name string,dates string,prince int) row format delimited fields terminated by ',';
-- 将本地的film_log3.log文件数据加载到film表
load data local inpath 'film_log3.log'into table film;
-- 查看film表数据的前十条
select * from film limit 10;
-- 查询2014年所有票房信息并将结果存储到新表“film_2014”中
create table film_2014 as select * from film where dates like'2014%';
-- 将2015年票房前十数据存储到linux下的“'/home/data'”目录下
insert overwrite local directory'/home/hadoop/data'select name,sum(prince)
 p from film where dates like'2015%' group by name order by p desc limit 10;
```

```scala
import org.apache.spark.sql.types._
import spark.implicits._
val name=StructField("name",StringType)
val prince=StructField("prince",DoubleType)
val dates=StructField("dates",StringType)
val data=Array(name,dates,prince)
val schema=StructType(data)
val files="film_log3.log"
val df=spark.read.schema(schema).option("sep",",").csv(files)
df.createOrReplaceTempView("film")
spark.sql("select name,sum(prince) p from film group by name order by p desc limit 10").collect.foreach(println)
```
