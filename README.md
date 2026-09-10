# README

## 项目介绍

HDFS（Hadoop Distributed File System, Hadoop分布式文件存储系统）能提供高吞吐量的数据访问，非常适合大规模数据集上的应用。HDFS是Apache Hadoop的一部分，Hadoop是一个开源的分布式存储及计算框架，被广泛用于海量数据的存储及处理。

当前代码仓仅含有修复patch，用于鲲鹏BoostKit大数据商用版本，基于代码仓`https://gitee.com/mirrors/hadoop.git` 分支 `rel/release-3.2.0 `。
包含两个patch：
1. 适配性patch：`https://gitee.com/kunpengcompute/boostkit-bigdata.git` 的 
`boostkit-bigdata/omnioperator/contrib/hadoop-3_2_0/hadoop-3_2_0.patch`
2. 修复性patch：`fix-hdfsThreadDestructor.patch`

## 编译方法

```shell
  git apply hadoop-3_2_0.patch  
  git apply fix-hdfsThreadDestructor.patch
  cd  hadoop/hadoop-hdfs-project/hadoop-hdfs-native-client/  
  sed -i "172d" src/CMakeLists.txt  
  sed -i "155d" src/CMakeLists.txt  
  mvn clean package -DskipTests -Pdist,native -Dtar   
```
产物位于 `hadoop/hadoop-hdfs-project/hadoop-hdfs-native-client/target/target/usr/local/lib/`