# 自定义测试：Tracks

## 定义
一条Track定义了一个或多个测试场景，具体的结构定义在另一个文件中

## 从存在集群创建

如果你已经有一个存有数据的es集群，你可以使用rally的子命令`create-track`创建一个Rally的track。如基于一个已经部署的es集群通过`products` 和 `companies`这两个索引创建一个tracks，命令如下：
```
esrally create-track --track=acme --target-hosts=127.0.0.1:9200 --indices="products,companies" --output-path=~/tracks
```
如果要连接到启用了TLS和基本身份验证的集群，需要指明`--client-options`代码如下：

```
esrally create-track --track=acme --target-hosts=abcdef123.us-central-1.gcp.cloud.es.io:9243 --client-options="timeout:60,use_ssl:true,verify_certs:true,basic_auth_user:'elastic',basic_auth_password:'secret-password'" --indices="products,companies" --output-path=~/tracks
```

需要将 `basic_auth_user` 和 `basic_auth_password` 进行相应的修改。track生成器将在指定的输出目录中创建一个具有track名称的文件夹：
```
> find tracks/acme
tracks/acme
tracks/acme/companies-documents.json
tracks/acme/companies-documents.json.bz2
tracks/acme/companies-documents-1k.json
tracks/acme/companies-documents-1k.json.bz2
tracks/acme/companies.json
tracks/acme/products-documents.json
tracks/acme/products-documents.json.bz2
tracks/acme/products-documents-1k.json
tracks/acme/products-documents-1k.json.bz2
tracks/acme/products.json
tracks/acme/track.json
```
文件组织如下：
* `track.json` 包含了实际的测试track
* `companies.json`和`products.json`包含提取的索引的映射和设置。
* `*-documents.json(.bz2)`包含提取索引中所有文档数据，带有-1k后缀的文件包含文档语料库的较小版本，以支持test模式。

## 从数据集构建
我们手把手的教你构建一个track`tutorial`我们将所有内容存储在`〜/rally-tracks/tutorial`目录中，你也可以选择其他任何位置。

首先获取数据，[Geonames](http://www.geonames.org/)根据[creative commons license](http://creativecommons.org/licenses/by/3.0/)许可提供地理数据。下载[allCountries.zip](http://download.geonames.org/export/dump/allCountries.zip)（大约300MB），解压缩并检查`allCountries.txt`。
该文件以制表符分隔，但要使用Elasticsearch批量索引数据，我们需要JSON格式的数据。使用以下脚本转换数据：
```
import json

cols = (("geonameid", "int", True),
        ("name", "string", True),
        ("asciiname", "string", False),
        ("alternatenames", "string", False),
        ("latitude", "double", True),
        ("longitude", "double", True),
        ("feature_class", "string", False),
        ("feature_code", "string", False),
        ("country_code", "string", True),
        ("cc2", "string", False),
        ("admin1_code", "string", False),
        ("admin2_code", "string", False),
        ("admin3_code", "string", False),
        ("admin4_code", "string", False),
        ("population", "long", True),
        ("elevation", "int", False),
        ("dem", "string", False),
        ("timezone", "string", False))


def main():
    with open("allCountries.txt", "rt", encoding="UTF-8") as f:
        for line in f:
            tup = line.strip().split("\t")
            record = {}
            for i in range(len(cols)):
                name, type, include = cols[i]
                if tup[i] != "" and include:
                    if type in ("int", "long"):
                        record[name] = int(tup[i])
                    elif type == "double":
                        record[name] = float(tup[i])
                    elif type == "string":
                        record[name] = tup[i]
            print(json.dumps(record, ensure_ascii=False))


if __name__ == "__main__":
    main()
```

把脚本存储为`toJSON.py`,放到tutorial文件夹底下（`~/rally-tracks/tutorial`）使用python命令执行它：`python3 toJSON.py > documents.json`

然后将以下映射文件作为`index.json`存储在tutorial目录中：
```
  "settings": {
    "index.number_of_replicas": 0
  },
  "mappings": {
    "docs": {
      "dynamic": "strict",
      "properties": {
        "geonameid": {
          "type": "long"
        },
        "name": {
          "type": "text"
        },
        "latitude": {
          "type": "double"
        },
        "longitude": {
          "type": "double"
        },
        "country_code": {
          "type": "text"
        },
        "population": {
          "type": "long"
        }
      }
    }
  }
}
```

 > **注意** 本教程假定您要对7.0.0之前的Elasticsearch版本进行基准测试。如果要对Elasticsearch 7.0.0或更高版本进行基准测试，则需要删除上面的映射类型。
 


有关es的语法的详细信息，请参阅有关[映射](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping.html)的Elasticsearch文档和[create index API](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-create-index.html)  
最后，将tarck存储为tutorial目录中的track.json:
```
{
  "version": 2,
  "description": "Tutorial benchmark for Rally",
  "indices": [
    {
      "name": "geonames",
      "body": "index.json",
      "types": [ "docs" ]
    }
  ],
  "corpora": [
    {
      "name": "rally-tutorial",
      "documents": [
        {
          "source-file": "documents.json",
          "document-count": 11658903,
          "uncompressed-bytes": 1544799789
        }
      ]
    }
  ],
  "schedule": [
    {
      "operation": {
        "operation-type": "delete-index"
      }
    },
    {
      "operation": {
        "operation-type": "create-index"
      }
    },
    {
      "operation": {
        "operation-type": "cluster-health",
        "request-params": {
          "wait_for_status": "green"
        }
      }
    },
    {
      "operation": {
        "operation-type": "bulk",
        "bulk-size": 5000
      },
      "warmup-time-period": 120,
      "clients": 8
    },
    {
      "operation": {
        "operation-type": "force-merge"
      }
    },
    {
      "operation": {
        "name": "query-match-all",
        "operation-type": "search",
        "body": {
          "query": {
            "match_all": {}
          }
        }
      },
      "clients": 8,
      "warmup-iterations": 1000,
      "iterations": 1000,
      "target-throughput": 100
    }
  ]
}
```

其中文档数可以通过`wc -l documents.json`获取，文档未压缩大小可以通过`ll`获取。

> **注意** 本教程假定您要对7.0.0之前的Elasticsearch版本进行基准测试。如果要对Elasticsearch 7.0.0或更高版本进行基准测试，则需要删除上面的types属性。

> **注意** 您可以将任何支持的脚本与track一起存储。但是，您需要将它们放置在以“ _”开头的目录中，例如“_support”。 Rally从任何目录加载跟踪插件（请参阅下文），但将忽略以“_”开头的目录。

> **注意**  我们为轨道定义了JSON模式，您可以使用它检查如何定义track。您还应该检查Rally提供的track以获取灵感。

当你运行`esrally list tracks --track-path=~/rally-tracks/tutorial`的时候，一条新的track就会出现：
```
dm@io:~ $ esrally list tracks --track-path=~/rally-tracks/tutorial

    ____        ____
   / __ \____ _/ / /_  __
  / /_/ / __ `/ / / / / /
 / _, _/ /_/ / / / /_/ /
/_/ |_|\__,_/_/_/\__, /
                /____/
Available tracks:

Name        Description                   Documents    Compressed Size  Uncompressed Size
----------  ----------------------------- -----------  ---------------  -----------------
tutorial    Tutorial benchmark for Rally      11658903  N/A              1.4 GB
```
您还可以通过以下方式显示有关track的详细信息：
`esrally info --track-path=~/rally-tracks/tutorial`
```
dm@io:~ $ esrally info --track-path=~/rally-tracks/tutorial

    ____        ____
   / __ \____ _/ / /_  __
  / /_/ / __ `/ / / / / /
 / _, _/ /_/ / / / /_/ /
/_/ |_|\__,_/_/_/\__, /
                /____/

Showing details for track [tutorial]:

* Description: Tutorial benchmark for Rally
* Documents: 11,658,903
* Compressed Size: N/A
* Uncompressed Size: 1.4 GB


Schedule:
----------

1. delete-index
2. create-index
3. cluster-health
4. bulk (8 clients)
5. force-merge
6. query-match-all (8 clients)
```

恭喜，您已经创建了第一条track！你可以用`esrally --distribution-version=6.0.0 --track-path=~/rally-tracks/tutorial`测试es集群了。

















