# 安装

这是Rally的详细安装指南。如果您很急，可以查看[快速开始](./quickstart.md)。

## 硬件需求

在产生负载的计算机上使用SSD。如果您运行bulk接口的测试，Rally将从磁盘读取一个或多个数据文件。通常，您将配置多个客户端，并且每个客户端读取数据文件的一部分。对于磁盘，这表现为随机访问模式，其中机械硬盘的性能较差。为避免客户端的读取出现瓶颈，因此应在每台负载生成器计算机上使用SSD。

## 准备工作

Rally不支持Windows，仅在MacOS和Linux上进行了有效测试。首先安装以下软件包。

### python
* 使用Python 3.5或更高版本的python3。验证：`python3 --version`
* Python3头文件（包含在Python3开发包中）。
* pip3可用。使用`pip3 --version`进行验证。

#### Debian / Ubuntu

```
sudo apt-get install gcc python3-pip python3-dev
```
#### RHEL / CentOS 6 and 7

请参考Red Hat Software Collections中关于Python 3.5的[安装说明](https://www.softwarecollections.org/en/scls/rhscl/rh-python35/)。

#### Amazon Linux

```
sudo yum install -y gcc python35-pip.noarch python35-devel.x86_64
```

#### MacOS

这里建议你使用[Homebrew](https://brew.sh/).
```
brew install python3
```

### git

如果满足一下条件则不需要git

* 您仅将Rally用作负载生成器（`--pipeline = benchmark-only`），或者通过`--team-path`引用Elasticsearch配置。
* 您创建自己的track，并使用--track-path对其进行引用。


在所有其他情况下，Rally需要git 1.9或更高版本。用`git --version`验证。

#### Debian / Ubuntu

```
sudo apt-get install git
```

#### Red Hat / CentOS / Amazon Linux

```
sudo yum install git
```

``` important:: 如果使用RHEL，请通过Red Hat Software Collections安装最新版本的git。
```