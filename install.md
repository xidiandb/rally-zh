# 安装

这是Rally的详细安装指南。如果您很急，可以查看[快速开始](quickstart.md)。

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

``` note:: 如果使用RHEL，请通过Red Hat Software Collections安装最新版本的git。
```

#### MacOS

`git` is already installed on MacOS.

### JDK

您要在其中启动Elasticsearch的所有计算机上都需要JDK。如果您仅将Rally用作负载生成器以对远程集群进行基准测试不需要JDK。有关如何安装JDK的详细信息，请查看操作系统的文档页面。  
为了找到JDK，Rally需要在所有目标机器上设置环境变量`JAVA_HOME`为了拥有更具体的控制权（例如，当您想对各种Elasticsearch版本进行基准测试时），您还可以设置JAVAx_HOME，其中x是JDK的主要版本（例如JAVA8_HOME将指向JDK 8安装）然后，Rally将选择每个可用版本的Elasticsearch支持的最高JDK。
``` note:: 如果您下载了Rally，请安装Elasticsearch的本地副本并对其进行基准测试（即默认Rally行为），并确保使用建议的内核设置配置Rally服务器的操作系统（OS） 
```

## 安装 Rally

只需使用pip安装Rally `pip3 install esrally`

``` note:: 根据您的系统设置，您可能需要在此命令前加上sudo。
```

如果在安装过程中出现错误，则可能是由于安装了psutil所致，我们用来收集系统指标（例如CPU利用率）确保已按照上述先决条件部分中的说明安装了Python开发包。

## Non-sudo 安装

如果您不想在安装Rally时使用sudo，则仍然可以安装，但要多花点时间：
1. 指定 `--user` 操作在装rally的时候 (step 2 above), 命令就像: `python3 setup.py develop --user`.

2. 检查安装脚本的输出或查找python文档变量`site.USER_BASE`找出脚本在哪在Linux上,一般为`~/.local/bin`.  

现在，您可以将〜/.local/bin添加到path中，也可以通过`〜/.local/bin/esrally`而不是`esrally`调用Rally。

## 虚拟环境安装

您也可以使用Virtualenv将Rally安装到隔离的Python环境中，而无需sudo。

1. 使用`virtualenv --python=python3`在目录中设置新的virtualenv环境。
2. 使用`source /path/to/virtualenv/dir/bin/activate`激活环境
3. 使用`pip install esrally`安装Rally

每当您想使用Rally时，请先运行激活脚本（上面的步骤2）。完成后，只需在shell中执行`deactivate`命令即可退出虚拟环境。

## PyEnv 安装

Rally可以使用不同的Python版本进行测试，并且使用pyenv对其进行管理。

请参阅PyEnv[安装说明](https://github.com/pyenv/pyenv#installation)。

## Docker

可以在DockerHub中找到Rally的[Docker镜像](https://hub.docker.com/r/elastic/rally)。

有关详细说明，请参阅“使用Docker运行Rally”。

## 离线安装

如果您在公司环境中服务器无法访问Internet，则可以使用Rally的脱机安装软件包。请按照以下步骤安装Rally：

1. 如上所述安装所有必备组件。
2. 下载最新版本的[安装软件包](https://github.com/elastic/rally/releases/latest)，然后将其复制到目标计算机。
3. 使用`tar -xzf esrally-dist-*.tar.gz`解压缩安装包
4. 使用`sudo ./esrally-dist-*/install.sh`运行安装脚本

## 下一步
安装Rally之后，您需要对其进行配置。只需运行`esrally configure`或按照配置帮助页面获取更多指导。
