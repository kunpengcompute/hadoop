# README

## 项目介绍

HDFS（Hadoop Distributed File System, Hadoop分布式文件存储系统）能提供高吞吐量的数据访问，非常适合大规模数据集上的应用。HDFS是Apache Hadoop的一部分，Hadoop是一个开源的分布式存储及计算框架，被广泛用于海量数据的存储及处理。

EC（Erasure Coding, 纠删码）是一种存储系统的数据保护方法，通过将原始的数据进行编码得到冗余，并将数据和冗余一并存储起来，达到容错的目的。将n块原始数据，通过编码得到m块冗余数据。当其中任意小于等于m块数据出错（包括原始数据和冗余数据）时，都可以通过重构算法恢复出原始的n块数据。

这里提供了一种针对华为鲲鹏920服务器的HDFS EC性能调优手段，可以提高HDFS再RS-6-3-1024k策略下的EC读写性能。

## 目录结构

```txt
├── docs                                    # 项目文档目录
│   ├── LICENSE-DOCS                        # 文档许可协议
│   └── zh                                  # 中文文档目录
│       ├── figures                         # 中文文档图片资料目录
│       ├── release_notes.md                # 版本说明
│       ├── installation_guide.md           # 安装指南
│       └── user_guide.md                   # 用户指南
├── hdfs-3.1.0-ksal-patch                   # 使能KSAL补丁文件
├── hdfs-3.1.0-testdfsio.patch              # 修改TESTDFSIO工具补丁文件
├── LICENSE                                 # 软件许可协议
└── README.md                               # 介绍文档
```

## 安装指南

环境要求、编译安装指南详见[安装指南](docs/zh/installation_guide.md)。

## 用户指南

KAE开关方法以及TestDFSIO工具指定模板文件方法详见[用户指南](docs/zh/user_guide.md)。

## 快速入门

在server节点执行以下命令。

```shell
$HADOOP_HOME/bin/hadoop jar \
  $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-client-jobclient-3.1.0-tests.jar \
  TestDFSIO \
  -write \
  -nrFiles 4000 \
  -size 150MB \
  -erasureCodePolicy RS-6-3-1024k \
  -compression org.apache.hadoop.io.compress.GzipCodec
```

> 其中：
> - -write：表示为写测试。
> - -nrFiles 4000：表示4000个文件。
> - -size 150MB：表示每个文件150MB。
> - -erasureCodePolicy RS-6-3-1024k ：表示使用RS-6-3-1024k EC策略。
> - -compression org.apache.hadoop.io.compress.GzipCodec ：表示使用Gzip压缩。

## 版本说明

版本信息详见[版本说明](docs/zh/release_notes.md)。

## 免责声明<a name="ZH-CN_TOPIC_0000002551702485"></a>

**致本项目使用者**

- 本项目仅供调试和开发之用，使用者需自行承担使用风险，并理解以下内容：
    - 数据处理及删除：用户在使用本工具过程中产生的数据属于用户责任范畴。建议用户在使用完毕后及时删除相关数据，以防信息泄露。
    - 数据保密与传播：使用者了解并同意不得将通过本工具产生的数据随意外发或传播。对于由此产生的信息泄露、数据泄露或其他不良后果，本工具及其开发者概不负责。
    - 用户输入安全性：用户需自行保证输入的命令行的安全性，并承担因输入不当而导致的任何安全风险或损失。对于输入命令行不当所导致的问题，本工具及其开发者概不负责。

- 免责声明范围：本免责声明适用于所有使用本工具的个人或实体。使用本工具即表示您同意并接受本声明的内容，并愿意承担因使用该功能而产生的风险和责任，如有异议请停止使用本工具。
- 在使用本工具之前，请**谨慎阅读并理解以上免责声明的内容**。对于使用本工具所产生的任何问题或疑问，请及时联系开发者。

**致数据所有者**

如果您不希望您的模型或数据集等信息在本项目中被提及，或希望更新本项目有关的描述，请在GitCode提交issue，我们将根据您的issue要求删除或更新您相关描述。衷心感谢您对本项目的理解和贡献。

## License<a name="ZH-CN_TOPIC_0000002520662484"></a>

本项目的代码适用于Apache License 2.0许可证，具体请参见[LICENSE文件](LICENSE)。

本项目的文档适用于CC-BY 4.0许可证，具体请参见[LICENSE文件](docs/LICENSE-DOCS)。

## 贡献声明<a name="ZH-CN_TOPIC_0000002551662481"></a>

欢迎大家为社区做贡献，如果使用过程中有任何问题/建议，或者需要反馈特性需求和bug报告，可以提交[Issues](https://gitcode.com/boostkit/community/blob/master/docs/contributor/issue-submit.md)联系我们，具体贡献方法可参考[这里](https://gitcode.com/boostkit/community/blob/master/docs/contributor/contributing.md)。同时也欢迎大家在[讨论专区](https://gitcode.com/boostkit/community/discussions)展开讨论交流。感谢您的支持。