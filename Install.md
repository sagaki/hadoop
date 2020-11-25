# Install

```dos
vi /etc/hostname
reboot
vi /etc/hosts
ssh-keygen
scp .ssh/id_rsa.pub master:.ssh/
cat id_rsa.pub >> authorized_keys
scp .ssh/id_rsa.pub slave:.ssh/
tar -zxvf hadoop
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
        <value>/opt/hadoop/tmp</value>
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
scp hadoop slave:
hdsf namenode -format
start-all.sh
```

```dos
tar -zxvf hive
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
hive/bin/hive
tar -zxvf spark
cp spark-env.sh.template spark-env.sh
vi spark-env.sh
```

```sh
export JAVA_HOME=/opt/jdk
export HADOOP_HOME=/opt/hadoop
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

```dos
scp -r /opt/spark slave:
spark/sbin/start-all.sh
```
