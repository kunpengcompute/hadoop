# 用户指南

## KAE使能/关闭

1. 参考[安装指南](installation_guide.md)，完成KAE的安装。

2. 在HDFS中使能KAE。

   在各个agent执行以下命令。

   ```shell
   cp /usr/local/kaezip/lib/lib* /usr/local/hadoop/lib/native
   ```

3. （可选）在HDFS中关闭KAE。

   在各个agent执行以下命令。

   ```shell
   rm -rf /usr/local/hadoop/lib/native/libkaezip.*
   rm -rf /usr/local/hadoop/lib/native/libz.*
   ```
   > 该步骤用于在使能KAE后关闭KAE特性，如需要在HDFS中使能KAE无需执行该步骤。

4. 重启HDFS。

   在server节点执行以下命令。
   ```shell
   /usr/local/hadoop/sbin/stop-all.sh
   /usr/local/hadoop/sbin/start-all.sh
   hdfs dfsadmin -safemode leave
   ```
   
## TestDFSIO工具指定压缩文件模板文件

1. 参考[安装指南](installation_guide.md)，合入hdfs-3.1.0-testdfsio.patch并完成Hadoop编译部署后。

2. 在所有服务其的hadoop运行目录下创建testFiles文件夹，用于存放测试模板文件。

   ```shell
   mkdir -p hadoop/testFiles
   ```

3. 将测试文件拷贝到testFiles文件夹下，创建软链接（以target_file为例）。

   ```shell
   mv target_file ../hadoop/testFiles
   cd ../hadoop/testFiles
   ln -s target_file test_io_file
   ```

4. 重启hadoop集群。

   ```shell
   /usr/local/hadoop/sbin/stop-all.sh
   /usr/local/hadoop/sbin/start-all.sh
   hdfs dfs-admin -safemode leave
   ```