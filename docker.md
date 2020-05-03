# 通过docker运行

rally可以通过[docker镜像](https://hub.docker.com/r/elastic/rally)运行

## 局限性
使用Docker镜像时，不支持以下Rally功能：

1. 分发负载测试驱动程序以应用多台计算机的测试。
2. 使用`benchmark-only`测试以外的其他 pipelines

## 快速开始

您可以通过一个简单的命令列出可用的tracks来测试Rally Docker镜像

```
$ docker run elastic/rally list tracks

    ____        ____
   / __ \____ _/ / /_  __
  / /_/ / __ `/ / / / / /
 / _, _/ /_/ / / / /_/ /
/_/ |_|\__,_/_/_/\__, /
                /____/

Available tracks:

Name           Description                                                                                                                                                                        Documents    Compressed Size    Uncompressed Size    Default Challenge        All Challenges
-------------  ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------  -----------  -----------------  -------------------  -----------------------  ---------------------------------------------------------------------------------------------------------------------------
geopoint       Point coordinates from PlanetOSM                                                                                                                                                   60,844,404   481.9 MB           2.3 GB               append-no-conflicts      append-no-conflicts,append-no-conflicts-index-only,append-fast-with-conflicts
eventdata      This benchmark indexes HTTP access logs generated based sample logs from the elastic.co website using the generator available in https://github.com/elastic/rally-eventdata-track  20,000,000   755.1 MB           15.3 GB              append-no-conflicts      append-no-conflicts
nested         StackOverflow Q&A stored as nested docs                                                                                                                                            11,203,029   663.1 MB           3.4 GB               nested-search-challenge  nested-search-challenge,index-only
so             Indexing benchmark using up to questions and answers from StackOverflow                                                                                                            36,062,278   8.9 GB             33.1 GB              append-no-conflicts      append-no-conflicts
geoshape       Shapes from PlanetOSM                                                                                                                                                              60,523,283   13.4 GB            45.4 GB              append-no-conflicts      append-no-conflicts
http_logs      HTTP server log data                                                                                                                                                               247,249,096  1.2 GB             31.1 GB              append-no-conflicts      append-no-conflicts,append-no-conflicts-index-only,append-sorted-no-conflicts,append-index-only-with-ingest-pipeline,update
geonames       POIs from Geonames                                                                                                                                                                 11,396,505   252.4 MB           3.3 GB               append-no-conflicts      append-no-conflicts,append-no-conflicts-index-only,append-sorted-no-conflicts,append-fast-with-conflicts
noaa           Global daily weather measurements from NOAA                                                                                                                                        33,659,481   947.3 MB           9.0 GB               append-no-conflicts      append-no-conflicts,append-no-conflicts-index-only
percolator     Percolator benchmark based on AOL queries                                                                                                                                          2,000,000    102.7 kB           104.9 MB             append-no-conflicts      append-no-conflicts
nyc_taxis      Taxi rides in New York in 2015                                                                                                                                                     165,346,692  4.5 GB             74.3 GB              append-no-conflicts      append-no-conflicts,append-no-conflicts-index-only,append-sorted-no-conflicts-index-only,update,append-ml
geopointshape  Point coordinates from PlanetOSM indexed as geoshapes                                                                                                                              60,844,404   470.5 MB           2.6 GB               append-no-conflicts      append-no-conflicts,append-no-conflicts-index-only,append-fast-with-conflicts
metricbeat     Metricbeat data                                                                                                                                                                    1,079,600    87.6 MB            1.2 GB               append-no-conflicts      append-no-conflicts
pmc            Full text benchmark with academic papers from PMC                                                                                                                                  574,199      5.5 GB             21.7 GB              append-no-conflicts      append-no-conflicts,append-no-conflicts-index-only,append-sorted-no-conflicts,append-fast-with-conflicts

-------------------------------
[INFO] SUCCESS (took 3 seconds)
-------------------------------
```

下一步，我们假设Elasticsearch在es01:9200上运行，并且可以从运行Rally Docker运行的主机上访问
使用以下命令在test-mode下运行nyc_taxis的tracks：
```
$ docker run elastic/rally --track=nyc_taxis --test-mode --pipeline=benchmark-only --target-hosts=es01:9200
```
``` note:: 我们不需要像通常的命令行调用中那样显式指定esrally。 Docker镜像中的entrypoint会自动执行此操作。
```

现在，考虑到上述限制，您就可以使用所有常规的Rally命令。

## 配置
Docker镜像的配置文件在`/rally/.rally/rally.ini`下,我们可以创建自己的配置文件并挂载使用：
```
docker run -v /home/<myuser>/custom_rally.ini:/rally/.rally/rally.ini elastic/rally ...
```

## 持久化

强烈建议将容器的`/rally/.rally`挂载出来，这将确保您在调用之间具有持久性，并且下载和提取的任何tracks都可以重用，从而减少了启动时间。
您需要确保UID为1000（尤其在OpenShift中，GID为0），以便Rally可以写入挂载的目录。  
如果您的挂载文件中不包含`rally.ini`，则容器会在首次运行时为您创建一个。

例子：
```
mkdir myrally
sudo chgrp 0 myrally

# First run will generate the rally.ini
docker run --rm -v $PWD/myrally:/rally/.rally elastic/rally --track=nyc_taxis --test-mode --pipeline=benchmark-only --target-hosts=es01:9200

    ____        ____
   / __ \____ _/ / /_  __
  / /_/ / __ `/ / / / / /
 / _, _/ /_/ / / / /_/ /
/_/ |_|\__,_/_/_/\__, /
                /____/

Running simple configuration. Run the advanced configuration with:

  esrally configure --advanced-config

* Setting up benchmark root directory in /rally/.rally/benchmarks
* Setting up benchmark source directory in /rally/.rally/benchmarks/src/elasticsearch

Configuration successfully written to /rally/.rally/rally.ini. Happy benchmarking!

More info about Rally:

* Type esrally --help
* Read the documentation at https://esrally.readthedocs.io/en/latest/
* Ask a question on the forum at https://discuss.elastic.co/c/elasticsearch/rally

# now run our benchmark
docker run --rm -v $PWD/myrally:/rally/.rally elastic/rally --track=nyc_taxis --test-mode --pipeline=benchmark-only --target-hosts=es01:9200

...

# inspect results
$ tree myrally/benchmarks/races/
myrally/benchmarks/races/
└── 1d81930a-4ebe-4640-a09b-3055174bce43
    └── race.json

1 directory, 1 file
```

万一您忘记挂载目录，Rally Docker镜像将为/rally/.rally创建一个匿名卷，以确保即使容器终止后日志和结果也能持久保存。
例如，执行我们之前的快速入门示例：
```
docker run elastic/rally --track=nyc_taxis --test-mode --pipeline=benchmark-only --target-hosts=es01:9200
docker volume ls
DRIVER              VOLUME NAME
local               96256462c3a1f61120443e6d69d9cb0091b28a02234318bdabc52b6801972199
```

为了进一步检查内容，我们可以将其绑定到另一个镜像中，例如：
```
$ docker run --rm -i -v=96256462c3a1f61120443e6d69d9cb0091b28a02234318bdabc52b6801972199:/rallyvolume -ti python:3.7.3-slim /bin/bash
root@9a7dd7b3d8df:/# cd /rallyvolume/
root@9a7dd7b3d8df:/rallyvolume# ls
root@9a7dd7b3d8df:/rallyvolume/.rally# ls
benchmarks  logging.json  logs      rally.ini
# head -4 benchmarks/races/1d81930a-4ebe-4640-a09b-3055174bce43/race.json
{
 "rally-version": "1.2.1.dev0",
 "environment": "local",
 "race-id": "1d81930a-4ebe-4640-a09b-3055174bce43",
```
## 一些细节

Rally以用户1000的身份运行，其文件使用uid：gid 1000：0进行安装（以支持[任意用户ID](https://docs.openshift.com/enterprise/3.1/creating_images/guidelines.html)）。

## 扩展

您还可以在现有映像之上创建自己的自定义Docker映像。以下示例显示了如何入门：

```
FROM elastic/rally:1.2.1
COPY --chown=1000:0 rally.ini /rally/.rally/
```
然后，您可以使用以下命令构建和测试镜像：
```
docker build --tag=custom-rally .
docker run -ti custom-rally list tracks
```
