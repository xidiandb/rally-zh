# 快速开始

Rally是为Unix开发的，并已在Linux和MacOS上进行了很多测试。 Rally支持在Windows上运行的基准Elasticsearch集群，但是Rally本身需要安装在运行Unix的计算机上。

## 下载安装

安装Python 3.5+（包括pip3，git 1.9+）和对应的JDK以运行Elasticsearch。确保JAVA_HOME指向该JDK。然后运行以下命令，如有必要，可以使用sudo作为前缀：  
`pip3 install esrally`
如果您有任何麻烦或需要更详细的说明，请参阅详细的安装指南。

## 配置
第一次运行rally的时候需要进行配置，如果使用默认配置	的话直接执行 `esrally configure`。

有关更多详细说明，请参阅配置指南。

## 进行第一次测试

下面让我们进行第一次测试
```
esrally --distribution-version=6.5.3
```
这将下载Elasticsearch 6.5.3并对其运行Rally的默认track——the geonames（地名） track。比赛结束后，测试报告如下：
```
------------------------------------------------------
    _______             __   _____
   / ____(_)___  ____ _/ /  / ___/_________  ________
  / /_  / / __ \/ __ `/ /   \__ \/ ___/ __ \/ ___/ _ \
 / __/ / / / / / /_/ / /   ___/ / /__/ /_/ / /  /  __/
/_/   /_/_/ /_/\__,_/_/   /____/\___/\____/_/   \___/
------------------------------------------------------

|   Lap |                                                          Metric |                   Task |     Value |    Unit |
|------:|----------------------------------------------------------------:|-----------------------:|----------:|--------:|
|   All |                      Cumulative indexing time of primary shards |                        |   54.5878 |     min |
|   All |              Min cumulative indexing time across primary shards |                        |   10.7519 |     min |
|   All |           Median cumulative indexing time across primary shards |                        |   10.9219 |     min |
|   All |              Max cumulative indexing time across primary shards |                        |   11.1754 |     min |
|   All |             Cumulative indexing throttle time of primary shards |                        |         0 |     min |
|   All |     Min cumulative indexing throttle time across primary shards |                        |         0 |     min |
|   All |  Median cumulative indexing throttle time across primary shards |                        |         0 |     min |
|   All |     Max cumulative indexing throttle time across primary shards |                        |         0 |     min |
|   All |                         Cumulative merge time of primary shards |                        |   20.4128 |     min |
|   All |                        Cumulative merge count of primary shards |                        |       136 |         |
|   All |                 Min cumulative merge time across primary shards |                        |   3.82548 |     min |
|   All |              Median cumulative merge time across primary shards |                        |    4.1088 |     min |
|   All |                 Max cumulative merge time across primary shards |                        |   4.38148 |     min |
|   All |                Cumulative merge throttle time of primary shards |                        |   1.17975 |     min |
|   All |        Min cumulative merge throttle time across primary shards |                        |    0.1169 |     min |
|   All |     Median cumulative merge throttle time across primary shards |                        |   0.26585 |     min |
|   All |        Max cumulative merge throttle time across primary shards |                        |  0.291033 |     min |
|   All |                       Cumulative refresh time of primary shards |                        |    7.0317 |     min |
|   All |                      Cumulative refresh count of primary shards |                        |       420 |         |
|   All |               Min cumulative refresh time across primary shards |                        |   1.37088 |     min |
|   All |            Median cumulative refresh time across primary shards |                        |    1.4076 |     min |
|   All |               Max cumulative refresh time across primary shards |                        |   1.43343 |     min |
|   All |                         Cumulative flush time of primary shards |                        |  0.599417 |     min |
|   All |                        Cumulative flush count of primary shards |                        |        10 |         |
|   All |                 Min cumulative flush time across primary shards |                        | 0.0946333 |     min |
|   All |              Median cumulative flush time across primary shards |                        |  0.118767 |     min |
|   All |                 Max cumulative flush time across primary shards |                        |   0.14145 |     min |
|   All |                                                Median CPU usage |                        |     284.4 |       % |
|   All |                                              Total Young Gen GC |                        |    12.868 |       s |
|   All |                                                Total Old Gen GC |                        |     3.803 |       s |
|   All |                                                      Store size |                        |   3.17241 |      GB |
|   All |                                                   Translog size |                        |   2.62736 |      GB |
|   All |                                                      Index size |                        |   5.79977 |      GB |
|   All |                                                   Total written |                        |   22.8536 |      GB |
|   All |                                          Heap used for segments |                        |   18.8885 |      MB |
|   All |                                        Heap used for doc values |                        | 0.0322647 |      MB |
|   All |                                             Heap used for terms |                        |   17.7184 |      MB |
|   All |                                             Heap used for norms |                        | 0.0723877 |      MB |
|   All |                                            Heap used for points |                        |  0.277171 |      MB |
|   All |                                     Heap used for stored fields |                        |  0.788307 |      MB |
|   All |                                                   Segment count |                        |        94 |         |
|   All |                                                  Min Throughput |           index-append |   38089.5 |  docs/s |
|   All |                                               Median Throughput |           index-append |   38613.9 |  docs/s |
|   All |                                                  Max Throughput |           index-append |   40693.3 |  docs/s |
|   All |                                         50th percentile latency |           index-append |   803.417 |      ms |
|   All |                                         90th percentile latency |           index-append |    1913.7 |      ms |
|   All |                                         99th percentile latency |           index-append |   3591.23 |      ms |
|   All |                                       99.9th percentile latency |           index-append |   6176.23 |      ms |
|   All |                                        100th percentile latency |           index-append |   6642.97 |      ms |
|   All |                                    50th percentile service time |           index-append |   803.417 |      ms |
|   All |                                    90th percentile service time |           index-append |    1913.7 |      ms |
|   All |                                    99th percentile service time |           index-append |   3591.23 |      ms |
|   All |                                  99.9th percentile service time |           index-append |   6176.23 |      ms |
|   All |                                   100th percentile service time |           index-append |   6642.97 |      ms |
|   All |                                                      error rate |           index-append |         0 |       % |
|   All |                                                            ...  |                    ... |       ... |     ... |
|   All |                                                            ...  |                    ... |       ... |     ... |
|   All |                                                  Min Throughput | large_prohibited_terms |         2 |   ops/s |
|   All |                                               Median Throughput | large_prohibited_terms |         2 |   ops/s |
|   All |                                                  Max Throughput | large_prohibited_terms |         2 |   ops/s |
|   All |                                         50th percentile latency | large_prohibited_terms |   344.429 |      ms |
|   All |                                         90th percentile latency | large_prohibited_terms |   353.187 |      ms |
|   All |                                         99th percentile latency | large_prohibited_terms |    377.22 |      ms |
|   All |                                        100th percentile latency | large_prohibited_terms |   392.918 |      ms |
|   All |                                    50th percentile service time | large_prohibited_terms |   341.177 |      ms |
|   All |                                    90th percentile service time | large_prohibited_terms |   349.979 |      ms |
|   All |                                    99th percentile service time | large_prohibited_terms |   374.958 |      ms |
|   All |                                   100th percentile service time | large_prohibited_terms |    388.62 |      ms |
|   All |                                                      error rate | large_prohibited_terms |         0 |       % |


----------------------------------
[INFO] SUCCESS (took 1862 seconds)
----------------------------------
```

## 下一步
现在，您可以检查如何运行基准测试，更好地了解如何解释测试报告中的数字或[开始创建自己的track](./add_tracks.md)。请务必检查一些提示和技巧，以帮助您了解如何解决Rally中的特定问题。