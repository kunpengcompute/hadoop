# 项目介绍<a name="ZH-CN_TOPIC_0000002475681785"></a>

HDFS（Hadoop Distributed File System，Hadoop分布式文件系统）是指被设计成适合运行在通用硬件上的分布式文件系统。HDFS能提供高吞吐量的数据访问，非常适合大规模数据集上的应用。HDFS是Apache Hadoop的一部分，Hadoop是一个开源的分布式存储及计算框架，被广泛用于海量数据的存储及处理，可以以可靠、高效、可伸缩的方式进行数据处理。EC（Erasure Coding，纠删码）是一种存储系统的数据保护方法，通过将原始的数据进行编码得到冗余，并将数据和冗余一并存储起来，以达到容错的目的。

Kunpeng-HDFS是鲲鹏参与Hadoop开源社区进行优化后的分布式文件系统，利用鲲鹏处理器KAE硬件加速、KSAL存储算法库、BiShengJDK和JDK编译参数调优，提高HDFS压缩和EC计算性能，适用RS-6-3-1024k。

# 版本说明<a name="ZH-CN_TOPIC_0000002475607873"></a>

**表 1**  版本说明

<a name="table154016110243"></a>
<table><thead align="left"><tr id="row74015192418"><th class="cellrowborder" valign="top" width="30.73%" id="mcps1.2.4.1.1"><p id="p12401515246"><a name="p12401515246"></a><a name="p12401515246"></a>Kunpeng HDFS</p>
</th>
<th class="cellrowborder" valign="top" width="27.16%" id="mcps1.2.4.1.2"><p id="p1340117112414"><a name="p1340117112414"></a><a name="p1340117112414"></a>开源HDFS</p>
</th>
<th class="cellrowborder" valign="top" width="42.11%" id="mcps1.2.4.1.3"><p id="p24012120244"><a name="p24012120244"></a><a name="p24012120244"></a>特性</p>
</th>
</tr>
</thead>
<tbody><tr id="row7401131102414"><td class="cellrowborder" valign="top" width="30.73%" headers="mcps1.2.4.1.1 "><p id="p184021011249"><a name="p184021011249"></a><a name="p184021011249"></a>v1.0.0</p>
</td>
<td class="cellrowborder" valign="top" width="27.16%" headers="mcps1.2.4.1.2 "><p id="p1240213132420"><a name="p1240213132420"></a><a name="p1240213132420"></a>3.1.0</p>
</td>
<td class="cellrowborder" valign="top" width="42.11%" headers="mcps1.2.4.1.3 "><p id="p1040216111241"><a name="p1040216111241"></a><a name="p1040216111241"></a>优化<span>RS-6-3-1024k</span><span>策略下HDFS EC读写性能</span></p>
</td>
</tr>
</tbody>
</table>

# 环境部署<a name="ZH-CN_TOPIC_0000002442167888"></a>





## 配置编译环境<a name="ZH-CN_TOPIC_0000002475714261"></a>

基础库、Maven、CMake、Protobuf及依赖包安装详细操作步骤见[鲲鹏社区](https://www.hikunpeng.com/document/detail/zh/kunpengbds/ecosystemEnable/Hadoop/kunpenghadoop_02_0006.html)，毕昇JDK安装详细操作步骤见[毕昇开源社区](https://gitee.com/openeuler/bishengjdk-8/wikis/%E6%AF%95%E6%98%87JDK%208%20%E5%AE%89%E8%A3%85%E6%8C%87%E5%8D%97?sort_id=2891179)。

## 安装KSAL<a name="ZH-CN_TOPIC_0000002442354454"></a>

所有的DataNode节点都需要安装KSAL，详细操作步骤如下。

1.  获取[isa-l-2.28.0.tar.gz](https://github.com/intel/isa-l/archive/refs/tags/v2.28.0.tar.gz)安装包。
2.  解压安装包。

    ```
    tar -zxf isa-l-2.28.0.tar.gz
    ```

3.  编译安装isa-l。

    ```
    cd isa-l-2.28.0
    ./autogen.sh 
    ./configure --prefix=/usr --libdir=/usr/lib64 
    make 
    make install 
    make -f Makefile.unx
    ```

4.  确认isa-l安装情况。

    ```
    ll /lib64/libisal.so*
    ```

5.  获取[BoostKit-KSAL\_1.10.0.zip](https://kunpeng-repo.obs.cn-north-4.myhuaweicloud.com/Kunpeng%20BoostKit/Kunpeng%20BoostKit%2024.0.0/BoostKit-KSAL_1.10.0.zip)安装包。
6.  解压安装包。

    ```
    unzip BoostKit-KSAL_1.10.0.zip
    ```

7.  安装rpm。

    ```
    rpm -ivh libksal-release-1.10.0.oe1.aarch64.rpm
    ```

8.  确认ksal安装情况。

    ```
    rpm -qi libksal
    ```

## 编译、部署Hadoop<a name="ZH-CN_TOPIC_0000002475714265"></a>

1.  合入KSAL补丁。

    ```
    cd hadoop-3.1.0
    patch -p1 < hdfs-ksal-0001.patch
    ```

2.  Hadoop编译、部署的详细步骤参考[鲲鹏社区](https://www.hikunpeng.com/document/detail/zh/kunpengbds/ecosystemEnable/Hadoop/kunpenghadoop_02_0011.html)。
3.  指定毕昇JDK（所有agent节点都需要进行修改）。
    1.  打开“hadoop-env.sh”。

        ```
        vi /usr/local/hadoop/etc/hadoop/hadoop-env.sh
        ```

    2.  修改JAVA\_HOME路径。

        ```
        export JAVA_HOME=/usr/local/bisheng-jdk1.8.0_422
        ```

4.  修改JVM参数（所有agent节点都需要进行修改）。
    1.  打开“mapred-site.xml”。

        ```
        vim /usr/local/hadoop/etc/hadoop/mapred-site.xml
        ```

    2.  修改mapreduce.map.java.opts、mapreduce.reduce.java.opts、mapred.child.java.opts配置项。

        ```
        <property>
             <name>mapreduce.map.java.opts</name>
             <value>-Xmx5530m -XX:CICompilerCount=2</value>
         </property>
         <property>
             <name>mapreduce.reduce.java.opts</name>
             <value>-Xmx2765m -XX:CICompilerCount=2</value>
         </property>
         <property>
             <name>mapred.child.java.opts</name>
             <value>-Xmx2048m -Xms2048m -XX:CICompilerCount=2</value>
         </property>
        ```

5.  重启Hadoop。

    ```
    /usr/local/hadoop/sbin/stop-all.sh 
    /usr/local/hadoop/sbin/start-all.sh 
    hdfs dfsadmin -safemode leave
    ```

## KAE使能<a name="ZH-CN_TOPIC_0000002442514338"></a>

1.  KAE安装的详细步骤参考[鲲鹏社区](https://www.hikunpeng.com/document/detail/zh/kunpengaccel/kae/usermanual/kunpengaccel_06_0015.html)。
2.  在HDFS中开启KAE。

    在所有的agent节点执行以下命令。

    ```
    cp /usr/local/kaezip/lib/lib* /usr/local/hadoop/lib/native
    ```

3.  （可选）在HDFS中关闭KAE。

    在所有agent节点执行以下命令。

    ```
    rm -rf /usr/local/hadoop/lib/native/libkaezip.* 
    rm -rf /usr/local/hadoop/lib/native/libz.*
    ```

4.  重启Hadoop。

    ```
    /usr/local/hadoop/sbin/stop-all.sh 
    /usr/local/hadoop/sbin/start-all.sh 
    hdfs dfsadmin -safemode leave
    ```

# 快速上手<a name="ZH-CN_TOPIC_0000002475687685"></a>

在server节点执行以下命令。

```
$HADOOP_HOME/bin/hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-client-jobclient-3.1.0-tests.jar TestDFSIO -write -nrFiles 4000 -size 150MB -erasureCodePolicy RS-6-3-1024k
```

其中：

-   -write：表示为写测试。
-   -nrFiles 4000：表示4000个文件。
-   -size 150MB：表示每个文件150MB。
-   -erasureCodePolicy RS-6-3-1024k ：表示使用RS-6-3-1024k EC策略。

结果输出如下。

```
----- TestDFSIO ----- : write
            Date & time: Fri Sep 13 10:39:42 CST 2024
        Number of files: 4000
 Total MBytes processed: 600000
      Throughput mb/sec: 23.26
 Average IO rate mb/sec: 24
  IO rate std deviation: 4.27
     Test exec time sec: 189.7
```

其中：

-   Date & time：测试结束时间。
-   Number of files：测试文件数量。
-   Total MBytes processed：测试数据总量，单位MB。
-   Throughput mb/sec：吞吐量，以MB/s为单位，计算方法为**测试数据总量/每个map任务实际写入数据的执行时间之和**。
-   Average IO rate mb/sec：平均IO速率，单位MB/s，计算方法为**每个map需要写入的数据量/每个map任务实际写入数据的执行时间之和/任务数。**
-   IO rate std deviation：IO速率标准差。
-   Test exec time sec：测试执行耗时，单位秒。

# 安装后验证<a name="ZH-CN_TOPIC_0000002475688049"></a>

在浏览器中输入URL地址，访问Hadoop Web页面，URL格式为“http://server1:50070”。

其中，“server1”填写server进程所在节点的IP地址。通过观察Live Nodes是否与agent数目相等、Dead Nodes是否为0，判断集群是否正常启动。

# 贡献指南<a name="ZH-CN_TOPIC_0000002475608269"></a>

如果使用过程中有任何问题，或者需要反馈特性需求和bug报告，可以提交issues联系我们，具体贡献方法可参考[这里](https://gitcode.com/boostkit/community/blob/master/docs/contributor/contributing.md)。

# 免责声明<a name="ZH-CN_TOPIC_0000002442327760"></a>

此代码仓计划参与Hadoop软件开源，仅作性能提升，编码风格遵照原生开源软件，继承原生开源软件安全设计，不破坏原生开源软件设计及编码风格和方式，软件的任何漏洞与安全问题，均由相应的上游社区根据其漏洞和安全响应机制解决。请密切关注上游社区发布的通知和版本更新。鲲鹏计算社区对软件的漏洞及安全问题不承担任何责任。

