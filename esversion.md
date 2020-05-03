# 支持的es版本

## 最低支持的版本
Rally 1.4.1可以对Elasticsearch 2.0.0及更高版本进行基准测试。
## 废止政策

最新版本的Rally可以对所有当前受支持的Elasticsearch版本进行基准测试。一旦Elasticsearch版本达到使用寿命，Rally将支持对相应版本进行基准测试两年。例如，Elasticsearch 1.7.x一直受支持到2017年1月16日。该日期之后的两年（2019年1月16日），Rally取消了对Elasticsearch 1.7.x的支持。在此日期之后的下一个Rally维护版本中将不再提供版本支持。

## 特定于版本的限制
以下功能至少需要Elasticsearch 5.0.0：

* 采用插件进行benchmark 
* 从源码构建es