# 结果对比:Tournaments

假设我们要分析改进对性能的影响。
首先，我们需要进行benchmark。例如：
```
esrally --track=pmc --revision=latest --user-tag="intention:baseline_github_1234"
```
上面我们根据Elasticsearch的最新源代码版本运行benchmark。我们可以使用命令行参数`--user-tag`提供一个键值对，以记录比赛的指标。

新的测试如下：
```
esrally --track=pmc --revision=current --user-tag="intention:reduce_alloc_1234"
```
在进行了两次比赛之后，我们想知道性能的对比。借助Rally，我们可以轻松分析两个测试的差异。首先，我们需要通过`esrally list races`来找到两个比赛:
```
dm@io:~ $ esrally list races

    ____        ____
   / __ \____ _/ / /_  __
  / /_/ / __ `/ / / / / /
 / _, _/ /_/ / / / /_/ /
/_/ |_|\__,_/_/_/\__, /
                /____/
Recent races:

Race ID                               Race Timestamp    Track    Track Parameters    Challenge            Car       User Tags
------------------------------------  ----------------  -------  ------------------  ------------------   --------  ------------------------------
beb154e4-0a05-4f45-ad9f-e34f9a9e51f7  20160518T122341Z  pmc                          append-no-conflicts  defaults  intention:reduce_alloc_1234
0bfd4542-3821-4c79-81a2-0858636068ce  20160518T112057Z  pmc                          append-no-conflicts  defaults  intention:baseline_github_1234
0cfb3576-3025-4c17-b672-d6c9e811b93e  20160518T101957Z  pmc                          append-no-conflicts  defaults
```

我们可以看到用户标签可以帮助我们识别两次测试。我们要比较两个最近的比赛，并在下一步中必须提供两个比赛ID：
```
dm@io:~ $ esrally compare --baseline=0bfd4542-3821-4c79-81a2-0858636068ce --contender=beb154e4-0a05-4f45-ad9f-e34f9a9e51f7

    ____        ____
   / __ \____ _/ / /_  __
  / /_/ / __ `/ / / / / /
 / _, _/ /_/ / / / /_/ /
/_/ |_|\__,_/_/_/\__, /
                /____/

Comparing baseline
  Race ID: 0bfd4542-3821-4c79-81a2-0858636068ce
  Race timestamp: 2016-05-18 11:20:57
  Challenge: append-no-conflicts
  Car: defaults

with contender
  Race ID: beb154e4-0a05-4f45-ad9f-e34f9a9e51f7
  Race timestamp: 2016-05-18 12:23:41
  Challenge: append-no-conflicts
  Car: defaults

------------------------------------------------------
    _______             __   _____
   / ____(_)___  ____ _/ /  / ___/_________  ________
  / /_  / / __ \/ __ `/ /   \__ \/ ___/ __ \/ ___/ _ \
 / __/ / / / / / /_/ / /   ___/ / /__/ /_/ / /  /  __/
/_/   /_/_/ /_/\__,_/_/   /____/\___/\____/_/   \___/
------------------------------------------------------
                                                  Metric    Baseline    Contender               Diff
--------------------------------------------------------  ----------  -----------  -----------------
                        Min Indexing Throughput [docs/s]       19501        19118  -383.00000
                     Median Indexing Throughput [docs/s]       20232      19927.5  -304.45833
                        Max Indexing Throughput [docs/s]       21172        20849  -323.00000
                               Total indexing time [min]     55.7989       56.335    +0.53603
                                  Total merge time [min]     12.9766      13.3115    +0.33495
                                Total refresh time [min]     5.20067      5.20097    +0.00030
                                  Total flush time [min]   0.0648667    0.0681833    +0.00332
                         Total merge throttle time [min]    0.796417     0.879267    +0.08285
               Query latency term (50.0 percentile) [ms]     2.10049      2.15421    +0.05372
               Query latency term (90.0 percentile) [ms]     2.77537      2.84168    +0.06630
              Query latency term (100.0 percentile) [ms]     4.52081      5.15368    +0.63287
        Query latency country_agg (50.0 percentile) [ms]     112.049      110.385    -1.66392
        Query latency country_agg (90.0 percentile) [ms]     128.426      124.005    -4.42138
       Query latency country_agg (100.0 percentile) [ms]     155.989      133.797   -22.19185
             Query latency scroll (50.0 percentile) [ms]     16.1226      14.4974    -1.62519
             Query latency scroll (90.0 percentile) [ms]     17.2383      15.4079    -1.83043
            Query latency scroll (100.0 percentile) [ms]     18.8419      18.4241    -0.41784
 Query latency country_agg_cached (50.0 percentile) [ms]     1.70223      1.64502    -0.05721
 Query latency country_agg_cached (90.0 percentile) [ms]     2.34819      2.04318    -0.30500
Query latency country_agg_cached (100.0 percentile) [ms]     3.42547      2.86814    -0.55732
            Query latency default (50.0 percentile) [ms]     5.89058      5.83409    -0.05648
            Query latency default (90.0 percentile) [ms]     6.71282      6.64662    -0.06620
           Query latency default (100.0 percentile) [ms]     7.65307       7.3701    -0.28297
             Query latency phrase (50.0 percentile) [ms]     1.82687      1.83193    +0.00506
             Query latency phrase (90.0 percentile) [ms]     2.63714      2.46286    -0.17428
            Query latency phrase (100.0 percentile) [ms]     5.39892      4.22367    -1.17525
                            Median CPU usage (index) [%]     668.025       679.15   +11.12499
                            Median CPU usage (stats) [%]      143.75        162.4   +18.64999
                           Median CPU usage (search) [%]       223.1        229.2    +6.10000
                                  Total Young Gen GC [s]      39.447       40.456    +1.00900
                                    Total Old Gen GC [s]       7.108        7.703    +0.59500
                                         Index size [GB]     3.25475      3.25098    -0.00377
                                      Total written [GB]     17.8434      18.3143    +0.47083
                             Heap used for segments [MB]     21.7504      21.5901    -0.16037
                           Heap used for doc values [MB]     0.16436      0.13905    -0.02531
                                Heap used for terms [MB]     20.0293      19.9159    -0.11345
                                Heap used for norms [MB]    0.105469    0.0935669    -0.01190
                               Heap used for points [MB]    0.773487     0.772155    -0.00133
                               Heap used for points [MB]    0.677795     0.669426    -0.00837
                                           Segment count         136          121   -15.00000
                     Indices Stats(90.0 percentile) [ms]     3.16053      3.21023    +0.04969
                     Indices Stats(99.0 percentile) [ms]     5.29526      3.94132    -1.35393
                    Indices Stats(100.0 percentile) [ms]     5.64971      7.02374    +1.37403
                       Nodes Stats(90.0 percentile) [ms]     3.19611      3.15251    -0.04360
                       Nodes Stats(99.0 percentile) [ms]     4.44111      4.87003    +0.42892
                      Nodes Stats(100.0 percentile) [ms]     5.22527      5.66977    +0.44450
```

