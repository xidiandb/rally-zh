# 配置

安装后，Rally必须配置一次。如果您在安装Rally之后就运行`esrally`，它将检测到配置文件丢失。
如果您想在任何时候重新配置Rally，只需再次运行`esrally configure`。

## 简单配置

默认情况下，Rally将运行一个更简单的配置例程，并自动检测尽可能多的设置或为您选择默认设置。如果需要更多控制，可以使用`esrally configure --advanced-config`运行Rally。
Rally可以从源代码或使用官方的二进制包构建Elasticsearch。如果您想Rally从源代码构建Elasticsearch，则只能用于基准化Elasticsearch 5.0及更高版本。原因是在Elasticsearch 5.0中，构建工具从Maven切换到了Gradle。由于Rally利用Gradle包装器，因此仅限于Elasticsearch 5.0及更高版本。

首先看一个列子，运行 `esrally`:
```
dm@io:~ $ esrally
    ____        ____
   / __ \____ _/ / /_  __
  / /_/ / __ `/ / / / / /
 / _, _/ /_/ / / / /_/ /
/_/ |_|\__,_/_/_/\__, /
                /____/

Running simple configuration. Run the advanced configuration with:

  esrally configure --advanced-config

* Setting up benchmark root directory in /Users/dm/.rally/benchmarks
* Setting up benchmark source directory in /Users/dm/.rally/benchmarks/src/elasticsearch

Configuration successfully written to /Users/dm/.rally/rally.ini. Happy benchmarking!

More info about Rally:

* Type esrally --help
* Read the documentation at https://esrally.readthedocs.io/en/latest/
* Ask a question on the forum at https://discuss.elastic.co/c/elasticsearch/rally
```

恭喜你，是时候运行第一个benchmark了！！

## 进阶配置

如果您需要对一些变量进行更多控制，或者想要将指标存储在专用的Elasticsearch指标存储中，则应运行高级配置例程。您可以随时使用esrally configure --advanced-config来调用它。

### 先决条件

使用高级配置时，您可以选择Rally不在内存中而是在专用Elasticsearch实例中存储其指标。因此，您还需要安装以下软件：

1. Elasticsearch：专用的Elasticsearch实例，充当Rally的指标存储。如果您不想自己进行设置，也可以使用Elastic Cloud。
2. 可选：Kibana（也包含在Elastic Cloud中）。

### 准备

首先安装Elasticsearch 6.0或更高版本。一个具有单个节点的简单的现成安装就足够了。Rally使用此实例存储指标数据。它将自行设置必要的索引。 Rally的配置过程将要求您提供此群集的主机和端口。

``` note:: Rally将为基准群集选择端口范围39200-39300（HTTP)和39300-39400（传输），因此请勿将此端口范围用于度量标准存储
```
可选但建议同时安装Kibana。但是，请注意，Rally不会自动配置Kibana。

### 配置选项

Rally会在高级设置中询问您一些其他事项：
* **Benchmark root directory ：** Rally将所有与基准测试相关的数据存储在此目录中，该数据可能占用数十GB。如果要使用专用分区，则可以在此处指定其他根目录

* **Elasticsearch project directory:**
这是Elasticsearch源所在的目录。如果您不想使用Elasticsearch进行开发，则可以保留默认设置，但是如果要对本地更改进行基准测试，则应将Rally指向您的项目目录。请注意，Rally将使用此目录中的Gradle Wrapper运行构建（它运行`./gradlew clean`和`./gradlew：distribution：tar：assemble`）

* **Metrics store type：** 你可以选择`in-memory` 或者 `elasticsearch`这要求您启动一个专用的Elasticsearch实例来存储指标，但给您更大的灵活性来分析结果。

* **Metrics store settings:** （仅适用于度量标准存储类型elasticsearch）：将连接详细信息提供给Elasticsearch度量标准存储。这应该是您仅用于Rally的实例，但它可能是一个很小的实例。具有默认设置的单节点群集应执行此操作。在Elasticsearch指标存储上使用自签名证书时，可以通过将`datastore.ssl.verification_mode`设置设置为`none`来关闭证书验证。或者，您可以在`datastore.ssl.certificate_authorities`中输入证书颁发机构的签名证书的路径。两种设置都是可选的

* **Name for this benchmark environment（仅适用于指标存储类型elasticsearch）：**
您可以将相同的指标存储用于多个环境（例如本地，持续集成等），因此可以通过选择不同的名称将指标与不同的环境分开。

Rally保留Elasticsearch安装的信息，包括所有的数据。这会占用大量磁盘空间，因此您应该定期擦除`〜/.rally/benchmarks/races`.



