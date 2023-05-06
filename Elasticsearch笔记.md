# ELK

* ELK是Elasticsearch、Logstash、Kibana(也称ELK Stack)三大开源软件的缩写

* Elasticsearch是个开源分布式搜索引擎，提供搜集、分析、存储数据三大功能。它的特点有：分布式，零配置，自动发现，索引自动分片，索引副本机制，restful风格接口，多数据源，自动搜索负载等

* Logstash 主要是用来日志的搜集、分析、过滤日志的工具，支持大量的数据获取方式。一般工作方式为c/s架构，client端安装在需要收集日志的主机上，server端负责将收到的各节点日志进行过滤、修改等操作在一并发往elasticsearch上去

* Kibana 也是一个开源和免费的工具，Kibana可以为 Logstash 和 ElasticSearch 提供的日志分析友好的 Web 界面，可以帮助汇总、分析和搜索重要数据日志

  ![](.\img\63.jpg)

# ES核心概念

## 索引(Index)

* 一个索引就是一个拥有几分相似特征的文档的集合。比如说，你可以有一个客户数据的索引，另一个产品目录的索引，还有一个订单数据的索引。一个索引由一个名字来标识（必须全部是小写字母），并且当我们要对这个索引中的文档进行索引、搜索、更新和删除的时候，都要使用到这个名字。在一个集群中，可以定义任意多的索引。

* 能搜索的数据必须索引，这样的好处是可以提高查询速度，比如：新华字典前面的目录就是索引的意思，目录可以提高查询速度

  > Elasticsearch索引的精髓：一切设计都是为了提高搜索的性能

## 类型(Type)

* 在一个索引中，你可以定义一种或多种类型

* 一个类型是你的索引的一个逻辑上的分类/分区，其语义完全由你来定。通常，会为具有一组共同字段的文档定义一个类型

  > 目前已不在支持类型

## 文档(Document)

* 一个文档是一个可被索引的基础信息单元，**也就是一条数据**
* 文档以JSON（Javascript  Object  Notation）格式来表示，而JSON是一个到处存在的互联网数据交互格式

## 字段(Field)

* 相当于是数据表的字段，对文档数据根据不同属性进行的分类标识

## 映射(Mapping)

* mapping是处理数据的方式和规则方面做一些限制，如：某个字段的数据类型、默认值、分析器、是否被索引等等。这些都是映射里面可以设置的，它就是处理ES里面数据的一些使用规则设置也叫做映射，按着最优规则处理数据对性能提高很大，因此才需要建立映射，并且需要思考如何建立映射才能对性能更好

## 分片(Shards)

* 一个索引可以存储超出单个节点硬件限制的大量数据。比如，一个具有10亿文档数据的索引占据1TB的磁盘空间，而任一节点都可能没有这样大的磁盘空间。或者单个节点处理搜索请求，响应太慢。为了解决这个问题，Elasticsearch提供了将索引划分成多份的能力，每一份就称之为分片。当你创建一个索引的时候，你可以指定你想要的分片的数量。每个分片本身也是一个功能完善并且独立的“索引”，这个“索引”可以被放置到集群中的任何节点上。
* 分片很重要，主要有两方面的原因
  * 允许你水平分割/扩展你的内容容量
  * 允许你在分片之上进行分布式的、并行的操作，进而提高性能/吞吐量

## 副本(Replicas)

* 在一个网络/云的环境里，失败随时都可能发生，在某个分片/节点不知怎么的就处于离线状态，或者由于任何原因消失了，这种情况下，有一个故障转移机制是非常有用并且是强烈推荐的。为此目的，Elasticsearch允许你创建分片的一份或多份拷贝，这些拷贝叫做复制分片(副本)
* 复制分片之所以重要，有两个主要原因
  * 在分片/节点失败的情况下，提供了高可用性。因为这个原因，注意到复制分片从不与`原/主要（original/primary）分片`置于同一节点上是非常重要的
  * 扩展你的搜索量/吞吐量，因为搜索可以在所有的副本上并行运行
* 总之，每个索引可以被分成多个分片。一个索引也可以被复制0次（意思是没有复制）或多次。一旦复制了，每个索引就有了主分片（作为复制源的原来的分片）和复制分片（主分片的拷贝）之别。分片和复制的数量可以在索引创建的时候指定。在索引创建之后，你可以在任何时候动态地改变复制的数量，但是你事后不能改变分片的数量。默认情况下，Elasticsearch中的每个索引被分片1个主分片和1个复制，这意味着，如果你的集群中至少有两个节点，你的索引将会有1个主分片和另外1个复制分片（1个完全拷贝），这样的话每个索引总共就有2个分片，我们需要根据索引需要确定分片个数

## 分配(Allocation)

* 将分片分配给某个节点的过程，包括分配主分片或者副本。如果是副本，还包含从主分片复制数据的过程。这个过程是由master节点完成的

## 数据格式

![](.\img\64.jpg)

# 基础操作(RESTful)

## 索引操作

> 创建索引

* **PUT**	localhost:9200/索引名

> 查询所有索引

* **GET**	local host:9200/_cat/indices?v

  |      表头      |                             含义                             |
  | :------------: | :----------------------------------------------------------: |
  |      head      | 当前服务器健康状态：<br />green(集群完整)、yellow(单点正常、集群不完整)、red(单点不正常) |
  |     status     |                      索引打开、关闭状态                      |
  |     index      |                            索引名                            |
  |      uuid      |                         索引统一编号                         |
  |      pri       |                          主分片数量                          |
  |      rep       |                           副本数量                           |
  |   docs.count   |                         可用文档数量                         |
  |  docs.deleted  |                    文档删除状态(逻辑删除)                    |
  |   store.size   |                 主分片和副分片整体占空间大小                 |
  | pri.store.size |                       主分片占空间大小                       |

> 查询单个索引

* **GET**	localhost:9200/索引名

> 删除索引

* **DELETE**	localhost:9200/索引名

## 文档操作

> 创建-POST

* **POST**	localhost:9200/索引名/_doc[/指定id]

  ```json
  {
      "字段":"值"
  }
  ```
  
  > **如果增加数据时明确数据主键，那么请求方式也可以为PUT**

> 主键查询

* **GET** 	localhost:9200/索引名/_doc/主键id

> 全查询

* **GET** 	localhost:9200/索引名/_search

> 完全匹配

* **GET**	localhost:9200/索引名/_search

  ```json
  {
      "query":{
          "match_phrase":{
              "字段":"值"
          }
      }
  }
  ```

> 修改文档	**和新增文档一样，如果请求体变化，会将原有的数据内容覆盖**

* **POST**	localhost:9200/索引名/_doc/主键id

  ```json
  {
      "字段":"值"
  }
  ```

> 修改字段

* **POST**	localhost:9200/索引名/_update/主键Id

  ```json
  {
      "doc":{
          "字段":"值"
      }
  }
  ```

> 删除文档

* **DELETE**	localhost:9200/索引名/_doc/主键id

> 条件删除文档

* **POST**	localhost:9200/索引名/_delete_by_query

  ```json
  {
      "query":{
          "match":{
              "字段":"值"
          }
      }
  }
  ```

## 映射操作

* **索引库中的映射，类似于数据库中的表结构**

> 创建索引映射

* **PUT**	localhost:9200/索引名/_mapping

  ```json
  {
      "properties":{
          "字段名":{
              "type":"text",
              "index":true
          }
      }
  }
  ```

* type：类型
  * String：类型
    * text：可分词
    * keyword：不可分词，数据会作为完整字段进行匹配
  * Numerical：数值类型
    * 基本数据类型：long、integer、short、byte、double、float、half_float
    * 浮点数的高精度类型：scaled_float
  * Date：日期类型
  * Array：数组类型
  * Object：对象
* index：是否索引，默认为`true`
  * true：字段会被索引，则可以用来进行搜索
  * false：字段不会被索引，不能用来搜素
* store：是否将数据进行独立存储，默认为`false`
  
  * 原始的文本会存储在_source里面，默认情况下其他提取出来的字段都不是独立存储的，是从source里面提取出来的
* analyzer：分词器

> 查看映射

* **GET**	localhost:9200/索引名/_mapping 

## 高级查询

> 查询所有文档

* **GET** 	localhost:9200/索引名/_search

  ```json
  {
      "query":{
          "match_all":{
              
          }
      }
  }
  ```

> 匹配查询,会把条件进行分词，然后进行查询，多个词条之间是or的关系

* **GET**	lolcahost:9200/索引名/_search

  ```json
  {
      "query":{
          "match":{
              "字段":"值"
          }
      }
  }
  ```

> 字段匹配查询   **multi_match与match类似，不同的是它可以在多个字段中查询**

* **GET**	lolcahost:9200/索引名/_search

  ```json
  {
      "query": {
          "multi_match": {
              "query": "值",
              "fields": ["字段1","字段2"]
          }
      }
  }
  ```

> 关键字精确查询	**不对查询条件进行分词**

* **GET**	lolcahost:9200/索引名/_search

  ```json
  {
      "query":{
          "term":{
              "字段":{
                  "value":"值"
              }
          }
      }
  }
  ```

>  多关键字精确查询，允许指定多值进行匹配

* **GET**	lolcahost:9200/索引名/_search

  ```json
  {
      "query":{
          "terms":{
              "字段":["值1","值2"]
          }
      }
  }
  ```

> 指定字段查询

* **GET** localhost:9200/索引名/_search

  ```json
  {
      "query":{
          "match_all":{
  
          }
      },
      "_source":["title"]
  }
  ```

> 过滤字段

* **GET** localhost:9200/索引名/_search

```json
{
    "_source":{
        "includes":["字段1","字段2"]
        //includes:想要显示的字段
        //excludes:不想要显示的字段
    },
    "query":{
        "terms":{
            "字段":["值"]
        }
    }
}
```

> 分页查询

* **GET**	lolcalhost:9200/索引名/_search

  ```json
  {
      "query":{
          "match_all":{
  
          }
      },
      "from":0,
      "size":2
  }
  ```

> 排序

* **GET**	lolcalhost:9200/索引名/_search

  ```json
  {
      "query":{
          "match_all":{
  
          }
      },
      "sort":[{
          "字段":{
              "order":"asc"
          }
      }]
  }
  ```

> 组合查询	
>
> `must`(必须)、`must_not`(必须不)、`should`(应该)

* **GET**	localhost:9200/索引名/_search

  ```json
  {
      "query":{
          "bool":{
              "must":[
                  {
                      "match":{
                          "字段":"值"
                      }
                  }
              ],
              "must_not":[
                  {
                      "match":{
                          "字段":"值"
                      }
                  }
              ],
              "should":[
                  {
                      "match":{
                          "字段":"值"
                      }
                  }
              ]
          }
      }
  }
  ```

> 查询-条件范围 	
>
> `gt`(大于)、`gte`(大于等于)、`lt`(小于)、`lte`(小于等于)

* **GET**	localhost:9200/索引名/_search

  ```json
  {
      "query":{
        "range":{
            "字段":{
                "gte":值
                "lte":值
            }
        }
      }
  }
  ```

> 聚合操作-统计

* **GET**	localhost:9200/索引名/_search

  ```json
  {
      "aggs":{//聚合操作
          "随意取":{//名称
              "terms":{//分组
                  "field":"字段"
              }
          }
      },
      "size":0//不显示其他
  }
  ```

> 聚合操作-平均

* **GET**	localhost:9200/索引名/_search

  ```json
  {
      "aggs":{//聚合操作
          "随意取":{//名称
              "avg":{//平均值
                  "field":"字段"
              }
          }
      },
      "size":0//不显示其他
  }
  ```

> 返回高亮数据

* **GET**	localhost:9200/索引名/_search

  ```json
  {
      "query":{
          "match_phrase":{
              "字段":"值"
          }
      },
      "highlight":{
  //        "pre_tags":"<font color='red'>",//前置标签
  //        "post_tags":"</font>",//后置标签
          "fields":{
              "字段":{}
          }
      }
  }
  ```

> 模糊查询

* **GET**	localhost:9200/索引名/_search

  ```json
  {
      "query":{
          "fuzzy":{
              "字段":{
                  "value":"值"
              }
          }
      }
  }
  ```

# Java API操作

> 引入依赖

```xml
<!--elasticsearch的客户端-->
<dependency>
    <groupId>org.elasticsearch.client</groupId>
    <artifactId>elasticsearch-rest-high-level-client</artifactId>
    <version>7.8.0</version>
</dependency>
<!--jackson-->
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.9.9</version>
</dependency>
```

> 创建客户端

```java
public class ESClientUtils {

    /**
     * 创建客户端
     * @return 客户端
     */
    public static RestHighLevelClient creatES() {
        return new RestHighLevelClient(RestClient.builder(new HttpHost("localhost", 9200, "http")));
    }
}

```

## 索引操作

> 创建索引

```java
//创建ES客户端
RestHighLevelClient client = ESClientUtils.creatES();

//创建新增索引请求对象
CreateIndexRequest request = new CreateIndexRequest("test");

//客户端发送请求
CreateIndexResponse response = client.indices().create(request, RequestOptions.DEFAULT);

//响应状态
boolean acknowledged = response.isAcknowledged();
System.out.println("索引操作：" + acknowledged);

//关闭ES客户端
client.close();
```

> 查询索引

```java
//创建ES客户端
RestHighLevelClient client = ESClientUtils.creatES();

//创建获取索引请求对象
GetIndexRequest request = new GetIndexRequest("test");

//客户端发送请求
GetIndexResponse response = client.indices().get(request, RequestOptions.DEFAULT);

//响应状态
System.out.println("别名："+response.getAliases());
System.out.println("映射："+response.getMappings());
System.out.println("设置："+response.getSettings());

//关闭ES客户端
client.close();
```

> 删除索引

```java
//创建ES客户端
RestHighLevelClient client = ESClientUtils.creatES();

//创建删除索引请求对象
DeleteIndexRequest request = new DeleteIndexRequest("test");

//客户端发送请求
AcknowledgedResponse response = client.indices().delete(request, RequestOptions.DEFAULT);

//响应状态
System.out.println("删除操作：" + response.isAcknowledged());

//关闭ES客户端
client.close();
```

## 文档操作

> 新增文档

```java
RestHighLevelClient client = ESClientUtils.creatES();

//创建新增文档请求对象
IndexRequest request = new IndexRequest();

//指定索引和主键id值
request.index("user").id("1001");

//实体类数据
User user = new User();
user.setName("雷小松");
user.setSex("男");
user.setAge(18);

//向ES插入数据，必须将数据转换为JSON格式
ObjectMapper mapper = new ObjectMapper();
//将用户实体类转换为Json
String userJson = mapper.writeValueAsString(user);

//把userJson放入请求体中
request.source(userJson, XContentType.JSON);

//客户端发送请求
IndexResponse response = client.index(request, RequestOptions.DEFAULT);

//查看结果
System.out.println(response.getResult());

client.close();
```

> 修改文档

```java
RestHighLevelClient client = ESClientUtils.creatES();

//创建更新文档请求对象
UpdateRequest request = new UpdateRequest();

//指定索引和主键id值
request.index("user").id("1001");

//设置请求体，对数据进行修改
request.doc(XContentType.JSON,"sex","女");

//客户端发送请求
UpdateResponse response = client.update(request, RequestOptions.DEFAULT);

//查看结果
System.out.println(response.getResult());

client.close();
```

> 查看文档数据

```java
RestHighLevelClient client = ESClientUtils.creatES();

//创建查询请求对象
GetRequest request = new GetRequest();

//指定索引和主键id值
request.index("user").id("1001");

//客户端发送请求
GetResponse response = client.get(request, RequestOptions.DEFAULT);

System.out.println(response.getSourceAsString());


client.close();
```

> 删除文档

```java
RestHighLevelClient client = ESClientUtils.creatES();

//创建删除请求对象
DeleteRequest request = new DeleteRequest();

//指定索引和主键id值
request.index("user").id("1001");

//客户端发送请求
DeleteResponse response = client.delete(request, RequestOptions.DEFAULT);

System.out.println(response.getResult());

client.close();
```

> 批量新增

```java
RestHighLevelClient client = ESClientUtils.creatES();

//创建批量请求对象
BulkRequest request = new BulkRequest();

//新增数据对象
IndexRequest source1 = new IndexRequest().index("user").id("1001").source(XContentType.JSON, "name", "zhangsan","age",30,"sex","男");
IndexRequest source2 = new IndexRequest().index("user").id("1002").source(XContentType.JSON, "name", "lisi","age",30,"sex","女");
IndexRequest source3 = new IndexRequest().index("user").id("1003").source(XContentType.JSON, "name", "wangwu","age",40,"sex","男");
IndexRequest source4 = new IndexRequest().index("user").id("1004").source(XContentType.JSON, "name", "wangwu1","age",40,"sex","女");
IndexRequest source5 = new IndexRequest().index("user").id("1005").source(XContentType.JSON, "name", "wangwu2","age",50,"sex","男");
IndexRequest source6 = new IndexRequest().index("user").id("1006").source(XContentType.JSON, "name", "wangwu3","age",50,"sex","男");

//包装数据
request.add(source1,source2,source3,source4,source5,source6);

//客户端发送请求
BulkResponse response = client.bulk(request, RequestOptions.DEFAULT);

System.out.println("处理时间："+response.getTook());
System.out.println(Arrays.toString(response.getItems()));

client.close();
```

> 批量删除

```java
RestHighLevelClient client = ESClientUtils.creatES();

//创建批量请求对象
BulkRequest request = new BulkRequest();

//删除数据对象
DeleteRequest source1 = new DeleteRequest().index("user").id("1001");
DeleteRequest source2 = new DeleteRequest().index("user").id("1002");
DeleteRequest source3 = new DeleteRequest().index("user").id("1003");

//包装数据
request.add(source1,source2,source3);

//客户端发送请求
BulkResponse response = client.bulk(request, RequestOptions.DEFAULT);

System.out.println("处理时间："+response.getTook());
System.out.println(Arrays.toString(response.getItems()));

client.close();
```

## 高级查询

> 查询所有数据

```java
RestHighLevelClient client = ESClientUtils.creatES();

//查询数据
SearchRequest request = new SearchRequest();
request.indices("user");

//查询索引中全部的数据
request.source(new SearchSourceBuilder().query(QueryBuilders.matchAllQuery()));

SearchResponse response = client.search(request, RequestOptions.DEFAULT);

SearchHits hits = response.getHits();

System.out.println(hits.getTotalHits());
System.out.println(response.getTook());

for (SearchHit hit : hits) {
    System.out.println(hit.getSourceAsString());
}
```

> 条件查询

```java
//条件查询
request.source(new SearchSourceBuilder().query(QueryBuilders.termQuery("age",30)));

SearchResponse response = client.search(request, RequestOptions.DEFAULT);

SearchHits hits = response.getHits();

System.out.println(hits.getTotalHits());
System.out.println(response.getTook());

for (SearchHit hit : hits) {
    System.out.println(hit.getSourceAsString());
}
```

> 分页查询

```java
//查询所有数据
SearchSourceBuilder builder = new SearchSourceBuilder().query(QueryBuilders.matchAllQuery());
//(当前页码-1)*每页显示数据条数
//起始页
builder.from(0);
//条数
builder.size(2);

request.source(builder);

SearchResponse response = client.search(request, RequestOptions.DEFAULT);

SearchHits hits = response.getHits();

System.out.println(hits.getTotalHits());
System.out.println(response.getTook());

for (SearchHit hit : hits) {
    System.out.println(hit.getSourceAsString());
}
```

> 排序查询

```java
SearchSourceBuilder builder = new SearchSourceBuilder().query(QueryBuilders.matchAllQuery());
//排序查询
builder.sort("age", SortOrder.DESC);
request.source(builder);

SearchResponse response = client.search(request, RequestOptions.DEFAULT);

SearchHits hits = response.getHits();

System.out.println(hits.getTotalHits());
System.out.println(response.getTook());

for (SearchHit hit : hits) {
    System.out.println(hit.getSourceAsString());
}
```

> 过滤字段

```java
SearchSourceBuilder builder = new SearchSourceBuilder().query(QueryBuilders.matchAllQuery());
//排除
String[] excludes={};
//包含
String[] includes={"name"};

builder.fetchSource(includes,excludes);

request.source(builder);

SearchResponse response = client.search(request, RequestOptions.DEFAULT);

SearchHits hits = response.getHits();

System.out.println(hits.getTotalHits());
System.out.println(response.getTook());

for (SearchHit hit : hits) {
    System.out.println(hit.getSourceAsString());
}
```

> 组合查询

```java
SearchSourceBuilder builder = new SearchSourceBuilder();
BoolQueryBuilder boolQueryBuilder = QueryBuilders.boolQuery();

boolQueryBuilder.should(QueryBuilders.matchQuery("age",30));
boolQueryBuilder.should(QueryBuilders.matchQuery("age",40));

builder.query(boolQueryBuilder);

request.source(builder);

SearchResponse response = client.search(request, RequestOptions.DEFAULT);

SearchHits hits = response.getHits();

System.out.println(hits.getTotalHits());
System.out.println(response.getTook());

for (SearchHit hit : hits) {
    System.out.println(hit.getSourceAsString());
}
```

> 范围查询

```java
SearchSourceBuilder builder = new SearchSourceBuilder();
RangeQueryBuilder rangeQuery = QueryBuilders.rangeQuery("age");

//大于等于
rangeQuery.gte(30);
//小于等于
rangeQuery.lte(40);


builder.query(rangeQuery);

request.source(builder);

SearchResponse response = client.search(request, RequestOptions.DEFAULT);

SearchHits hits = response.getHits();

System.out.println(hits.getTotalHits());
System.out.println(response.getTook());

for (SearchHit hit : hits) {
    System.out.println(hit.getSourceAsString());
}
```

> 模糊查询

```java
SearchSourceBuilder builder = new SearchSourceBuilder();
//模糊查询条件，相差一个字
FuzzyQueryBuilder fuzziness = QueryBuilders.fuzzyQuery("name", "wangwu").fuzziness(Fuzziness.ONE);

builder.query(fuzziness);

request.source(builder);

SearchResponse response = client.search(request, RequestOptions.DEFAULT);

SearchHits hits = response.getHits();

System.out.println(hits.getTotalHits());
System.out.println(response.getTook());

for (SearchHit hit : hits) {
    System.out.println(hit.getSourceAsString());
}
```

> 高亮查询

```java
SearchSourceBuilder builder = new SearchSourceBuilder();
//限制条件
TermQueryBuilder termQueryBuilder = QueryBuilders.termQuery("name", "zhangsan");

//高亮操作
HighlightBuilder highlightBuilder = new HighlightBuilder();

//前置标签
highlightBuilder.preTags("<font color='red'>");
//后置标签
highlightBuilder.postTags("</font>");
//对谁高亮
highlightBuilder.field("name");

builder.highlighter(highlightBuilder);

builder.query(termQueryBuilder);

request.source(builder);

SearchResponse response = client.search(request, RequestOptions.DEFAULT);

SearchHits hits = response.getHits();

System.out.println(hits.getTotalHits());
System.out.println(response.getTook());

for (SearchHit hit : hits) {
    System.out.println(hit.getSourceAsString());
}
```

> 聚合查询

```java
SearchSourceBuilder builder = new SearchSourceBuilder();
//最大值
MaxAggregationBuilder maxAggregationBuilder = AggregationBuilders.max("maxAge").field("age");

builder.aggregation(maxAggregationBuilder);

request.source(builder);

SearchResponse response = client.search(request, RequestOptions.DEFAULT);

SearchHits hits = response.getHits();

System.out.println(hits.getTotalHits());
System.out.println(response.getTook());

for (SearchHit hit : hits) {
    System.out.println(hit.getSourceAsString());
}
```

> 分组查询



```java
SearchSourceBuilder builder = new SearchSourceBuilder();

TermsAggregationBuilder termsAggregationBuilder = AggregationBuilders.terms("ageGroup").field("age");

builder.aggregation(termsAggregationBuilder);

request.source(builder);

SearchResponse response = client.search(request, RequestOptions.DEFAULT);

SearchHits hits = response.getHits();

System.out.println(hits.getTotalHits());
System.out.println(response.getTook());

for (SearchHit hit : hits) {
    System.out.println(hit.getSourceAsString());
}

```

# 集群

## 单机

* 单台Elasticsearch服务器提供服务，往往都有最大的负载能力，超过这个阈值，服务器性能就会大大降低甚至不可用，所以生产环境中，一般都是运行在指定服务器集群中。除了负载能力，单点服务器也存在其他问题：
  * 单台机器存储容量有限
  * 单服务器容易出现单点故障，无法实现高可用
  * 单服务的并发处理能力有限

## 集群

> 在集群中，节点的状态有三种：绿色、黄色、红色

* 绿色：节点运行状态为健康状态。所有的主分片、副本分片都可以正常工作。
* 黄色：表示节点的运行状态为警告状态，所有的主分片目前都可以直接运行，但是至少有一个副本分片是不能正常工作的。
* 红色：表示集群无法正常工作



* 包含一个或多个具有相同 cluster.name 的节点.
* 集群内节点协同工作，共享数据，并共同分担工作负荷
* 由于节点是从属集群的，集群会自我重组来均匀地分发数据
* cluster Name是很重要的，因为每个节点只能是群集的一部分，当该节点被设置为相同的名称时，就会自动加入群集
* 集群中通过选举产生一个mater节点，它将负责管理集群范畴的变更，例如创建或删除索引，添加节点到集群或从集群删除节点。master 节点无需参与文档层面的变更和搜索，这意味着仅有一个 master 节点并不会因流量增长而成为瓶颈。任意一个节点都可以成为 master 节点。我们例举的集群只有一个节点，因此它会扮演 master 节点的角色
* 作为用户，我们可以访问包括 master 节点在内的集群中的任一节点。每个节点都知道各个文档的位置，并能够将我们的请求直接转发到拥有我们想要的数据的节点。无论我们访问的是哪个节点，它都会控制从拥有数据的节点收集响应的过程，并返回给客户端最终的结果。这一切都是由 Elasticsearch 透明管理的

## 节点

* 集群中包含很多服务器，一个节点就是其中的一个服务器。作为集群的一部分，它存储数据，参与集群的索引和搜索功能。
* 一个节点也是由一个名字来标识的，默认情况下，这个名字是一个随机生成的名字，这个名字会在启动的时候赋予节点。这个名字对于管理工作来说挺重要的，因为在这个管理过程中，你会去确定网络中的哪些服务器对应于Elasticsearch集群中的哪些节点。
* 一个节点可以通过配置集群名称的方式来加入一个指定的集群。默认情况下，每个节点都会被安排加入到一个叫做“elasticsearch”的集群中，这意味着，如果你在你的网络中启动了若干个节点，并假定它们能够相互发现彼此，它们将会自动地形成并加入到一个叫做“elasticsearch”的集群中
* 在一个集群里，只要你想，可以拥有任意多个节点。而且，如果当前你的网络中没有运行任何Elasticsearch节点，这时启动一个节点，会默认创建并加入一个叫做“elasticsearch”的集群

## Windows集群配置

1. 创建三个文件夹

   ```
   node-1001
   node-1002
   node-1003
   ```

2. 修改每个集群文件的配置文件

   * node-1001

     ```yaml
     # 集群名称，节点之间要保持一致
     cluster.name: my-elasticsearch
     
     # 节点名称，集群内要唯一
     node.name: node-1001
     
     # 是不是有资格成为主节点
     node.master: ture
     node.data: ture
     
     # ip地址
     network.host: localhost
     # http端口
     http.port: 1001
     # tcp监听端口
     transport.tcp.port: 9301
     
     # 跨域配置
     http.cors.enabled: ture
     http.cors.allow-origin: "*"
     ```

   * node-1002

     ```yaml
     # 集群名称，节点之间要保持一致
     cluster.name: my-elasticsearch
     
     # 节点名称，集群内要唯一
     node.name: node-1002
     
     # 是不是有资格成为主节点
     node.master: ture
     node.data: ture
     
     # ip地址
     network.host: localhost
     # http端口
     http.port: 1002
     # tcp监听端口
     transport.tcp.port: 9302
     
     # 发现其他节点
     discovery.seed_hosts: ["localhost:9301"]
     discovery.zen.fd.ping_timeout: 1m
     discovery.zen.fd.ping_retries: 5
     
     
     # 跨域配置
     http.cors.enabled: ture
     http.cors.allow-origin: "*"
     ```

   * node-1003

     ```yaml
     # 集群名称，节点之间要保持一致
     cluster.name: my-elasticsearch
     
     # 节点名称，集群内要唯一
     node.name: node-1002
     
     # 是不是有资格成为主节点
     node.master: ture
     node.data: ture
     
     # ip地址
     network.host: localhost
     # http端口
     http.port: 1002
     # tcp监听端口
     transport.tcp.port: 9302
     
     # 发现其他节点
     discovery.seed_hosts: ["localhost:9301","localhost:9302"]
     discovery.zen.fd.ping_timeout: 1m
     discovery.zen.fd.ping_retries: 5
     
     
     # 跨域配置
     http.cors.enabled: ture
     http.cors.allow-origin: "*"
     ```

* 查看集群状态
  * **GET** 	localhost:1001/_cluster/health

# 分布式集群

## 故障转移

* 当集群中只有一个节点在运行时，意味着会有一个单点故障问题——没有冗余。幸运的是，我们只需再启动一个节点即可防止数据丢失。当你在同一台机器上启动了第二个节点时，只要它和第一个节点有同样的`cluster.name` 配置，它就会自动发现集群并加入到其中。但是在不同机器上启动节点的时候，为了加入到同一集群，你需要配置一个可连接到的单播主机列表。之所以配置为使用单播发现，以防止节点无意中加入集群。只有在同一台机器上运行的节点才会自动组成集群。如果启动了第二个节点，我们的集群将会拥有两个节点的集群: 所有主分片和副本分片都已被分配通过
* elasticsearch-head插件查看集群情况集群健康值:green( 6 of 6 ) : 表示所有6个分片（包括3个主分片和3个副本分片）都在正常运行。: 3个主分片正常: 当第二个节点加入到集群后，3个副本分片将会分配到这个节点上——每个主分片对应一个副本分片。这意味着当集群内任何一个节点出现问题时，我们的数据都完好无损。所有新增索引的文档都将会保存在主分片上，然后被并行的复制到对应的副本分片上。这就保证了我们既可以从主分片又可以从副本分片上获得文档。

## 水平扩容

* 当启动了第三个节点，我们的集群将会拥有三个节点的集群: 为了分散负载而对分片进行重新分配

* 每个节点的硬件资源（CPU,  RAM,  I/O）将被更少的分片所共享，每个分片的性能将会得到提升

* 分片是一个功能完整的搜索引擎，它拥有使用一个节点上的所有资源的能力。我们这个拥有6个分片（3个主分片和3个副本分片）的索引可以最大扩容到6个节点，每个节点上存在一个分片，并且每个分片拥有所在节点的全部资源

* 主分片的数目在索引创建时就已经确定了下来。实际上，这个数目定义了这个索引能够存储的最大数据量。（实际大小取决于你的数据、硬件和使用场景。）但是，读操作、搜索和返回数据可以同时被主分片或副本分片所处理，所以当你拥有越多的副本分片时，也将拥有越高的吞吐量

  > 在运行中的集群上是可以动态调整副本分片数目的，我们可以按需伸缩集群。让我们把副本数从默认的1 增加到2

  * GET localhost:9200/索引名/_settings

    ```json
    {
        "number_of_replicas":2
    }
    ```

## 路由计算

> shard=hash(routing)%number_of_primary_shards

routing 是一个可变值，默认是文档的_id ，也可以设置成一个自定义的值。routing 通过hash 函数生成一个数字，然后这个数字再除以number_of_primary_shards （主分片的数量）后得到的余数，这个分布在0 到number_of_primary_shards-1 之间的余数，就是我们所寻求的文档所在分片的位置

## 分片控制

* 可以发送请求到集群中的任一节点。每个节点都有能力处理任意请求。每个节点都知道集群中任一文档位置，所以可以直接将请求转发到需要的节点上
* 用户可以访问任何一个节点获取数据，这个节点称之为**协调节点**
* 当发送请求的时候，为了扩展负载，更好的做法是轮询集群中所有的节点

## 数据写流程

![](.\img\65.jpg)

## 数据读流程

![](.\img\66.jpg)

## 正向索引&倒排索引

> 正向索引，就是搜索引擎会将待搜索的文件都对应一个文件ID，搜索时将这个ID和搜索关键字进行对应，形成K-V对，然后对关键字进行统计技术，但是互联网上收录在搜索引擎中文档的数目是个天文数字，这样的索引结构根本无法满足实时返回排名结果的要求。所以搜索引擎会将正向索引重新构建为倒排索引，**即把文件ID对应到关键词的映射，转换为关键词到文件ID的映射**，每个关键词都对应着一系列的文件，这些文件中都出现这个关键词。
>
> 一个倒排索引由文档中所有不重复词的列表构成，对于其中每个词，有一个包含它的文档列表

![](.\img\67.jpg)

> 将文档中的内容，拆分成单独的词，称之为**词条**

## 文档分析

* 将一块文本分成适合于倒排索引的独立的**词条**
* 将这些词条统一化为标准格式以提高他们的“可搜索性”，或者recall分析器执行上面的工作

* 分析器实际上是将三个功能封装到一个包里：
  * 字符过滤器
    * 首先，字符串按顺序通过每个**字符过滤器**。他们的任务是在分词前整理字符串。一个字符过滤器可以用来去掉HTML，或者将&转化为and
  * 分词器
    * 字符串会被**分词器**分为单个的词条。一个简单的分词器遇到空格和标点的时候，可能会将文本拆分成词条
  * Token过滤器
    * 最后，词条按顺序通过每个Token过滤器。这个过程可能会改变词条，删除词条，或者增加词条

> 内置分析器

* 标准分析器

  * 标准分析器是Elasticsearch默认使用的分析器。它是分析各种语言文本最常用的选择。它根据Unicode 联盟定义的单词边界划分文本。删除绝大部分标点。最后，将词条小写

    ```
    "Set the shape to semi-transparent by calling set_trans(5)"
    
    set, the, shape, to, semi, transparent, by, calling, set_trans, 5
    ```

* 简单分析器

  * 简单分析器在任何不是字母的地方分隔文本，将词条小写。它会产生

    ```
    "Set the shape to semi-transparent by calling set_trans(5)"
    
    set, the, shape, to, semi, transparent, by, calling, set, trans
    ```

* 空格分析器

  * 空格分析器在空格的地方划分文本

    ```
    "Set the shape to semi-transparent by calling set_trans(5)"
    
    Set, the, shape, to, semi-transparent, by, calling, set_trans(5)
    ```

* 语言分析器

  * 特定语言分析器可用于很多语言。它们可以考虑指定语言的特点

  * 例如，英语分析器附带了一组英语无用词（常用单词，例如and 或者the ，它们对相关性没有多少影响），它们会被删除。由于理解英语语法的规则，这个分词器可以提取英语单词的词干

    ```
    "Set the shape to semi-transparent by calling set_trans(5)"
    
    set, shape, semi, transpar, call, set_tran, 5
    ```

> 测试分析器

* **GET**	localhost:9200/_analyze

  ```json
  {
      "analyzer": "standard",//指定分析器
      "text": "被拆分的文本"
  }
  ```

> IK分词器

* 下载解压到ES根目录下的plugins目录下

* **GET**	localhost:9200/_analyze

  ```json
  {
      "text":"测试单词",
      "analyzer":"ik_max_word"
  }
  ```

  * ik_max_word：会将文本做最细粒度的拆分
  * ik_smart：会将文本做最粗粒度的拆分

> 自定义分词器

* **PUT**	localhost:9200/my_index

  ```json
  {
      "settings": {
          "analysis": {
              "char_filter": {
                  "&_to_and": {
                      "type":"mapping",
                      "mappings": [ "&=> and "]
                  }
              },
              "filter": {
                  "my_stopwords": {
                      "type":"stop",
                      "stopwords": [ "the", "a" ]
                  }
              },
              "analyzer": {
                  "my_analyzer": {
                      "type":"custom",
                      "char_filter":  [ "html_strip", "&_to_and" ],
                      "tokenizer":"standard",
                      "filter":[ "lowercase", "my_stopwords" ]
                  }
              }
          }
      }
  }
  ```

# 优化

## 分片策略

* 分片和副本的设计为ES 提供了支持分布式和故障转移的特性，但并不意味着分片和副本是可以无限分配的。而且索引的分片完成分配后由于索引的路由机制，我们是不能重新修改分片数的
  * 一个分片的底层即为一个Lucene 索引，会消耗一定文件句柄、内存、以及CPU 运转
  * 每一个搜索请求都需要命中索引中的每一个分片，如果每一个分片都处于不同的节点还好，但如果多个分片都需要在同一个节点上竞争使用相同的资源就有些糟糕了
  * 用于计算相关度的词项统计信息是基于分片的。如果有许多分片，每一个都只有很少的数据会导致很低的相关度

* 遵循原则
  * 控制每个分片占用的硬盘容量不超过ES的最大JVM的堆空间设置（一般设置不超过32G，参考下文的JVM设置原则），因此，如果索引的总容量在500G左右，那分片大小在16个左右即可；当然，最好同时考虑原则2
  * 考虑一下node数量，一般一个节点有时候就是一台物理机，如果分片数过多，大大超过了节点数，很可能会导致一个节点上存在多个分片，一旦该节点故障，即使保持了1个以上的副本，同样有可能会导致数据丢失，集群无法恢复。所以，**一般都设置分片数不超过节点数的3倍**
  * 主分片，副本和节点最大数之间数量，我们分配的时候可以参考以下关系
    * 节点数<=主分片数*（副本数+1）

> 推迟分片分配

* 对于节点瞬时中断的问题，默认情况，集群会等待一分钟来查看节点是否会重新加入，如果这个节点在此期间重新加入，重新加入的节点会保持其现有的分片数据，不会触发新的分片分配。这样就可以减少ES 在自动再平衡可用分片时所带来的极大开销。

* 通过修改参数delayed_timeout ，可以延长再均衡的时间，可以全局设置也可以在索引级别进行修改

  * **GET**	/_all/__settings

    ```json
    {
        "settings": {
            "index.unassigned.node_left.delayed_timeout": "5m" 
        }
    }
    ```

## 路由选择

> shard = hash(routing) % number_of_primary_shards

* routing默认值是文档的id，也可以采用自定义值

> 不带routing查询

* 在查询的时候因为不知道要查询的数据具体在哪个分片上，所以整个过程分为2 个步骤]
  * 分发：请求到达协调节点后，协调节点将查询请求分发到每个分片上
  * 聚合: 协调节点搜集到每个分片上查询结果，在将查询的结果进行排序，之后给用户返回结果

> 带routing查询

* 查询的时候，可以直接根据routing 信息定位到某个分配查询，不需要查询所有的分配，经过协调节点排序

## 写入速度优化

* ES的默认配置，是综合了数据可靠性、写入速度、搜索实时性等因素。针对于搜索性能要求不高，但是对写入要求较高的场景，我们需要尽可能的选择恰当写优化策略。综合来说，可以考虑以下几个方面来提升写索引的性能
  * 加大Translog Flush ，目的是降低Iops、Writeblock
  * 增加Index Refresh 间隔，目的是减少Segment Merge 的次数
  * 调整Bulk 线程池和队列
  * 优化节点间的任务分布
  * 优化Lucene 层的索引建立，目的是降低CPU 及IO

> 批量数据提交

* ES 提供了Bulk  API 支持批量操作，当我们有大量的写任务时，可以使用Bulk 来进行批量写入
* 通用的策略如下：Bulk 默认设置批量提交的数据量不能超过100M。数据条数一般是根据文档的大小和服务器性能而定的，但是单次批处理的数据大小应从5MB～15MB 逐渐增加，当性能没有提升时，把这个数据量作为最大值

> 优化存储设备

* ES 是一种密集使用磁盘的应用，在段合并的时候会频繁操作磁盘，所以对磁盘要求较高，当磁盘速度提升之后，集群的整体性能会大幅度提高

> 合理使用合并

* Lucene 以段的形式存储数据。当有新的数据写入索引时，Lucene 就会自动创建一个新的段
* 随着数据量的变化，段的数量会越来越多，消耗的多文件句柄数及CPU 就越多，查询效率就会下降
* 由于Lucene 段合并的计算量庞大，会消耗大量的I/O，所以ES 默认采用较保守的策略，让后台定期进行段合并

> 减少Refresh的次数

* Lucene 在新增数据时，采用了延迟写入的策略，默认情况下索引的refresh_interval 为1 秒
* Lucene 将待写入的数据先写到内存中，超过1 秒（默认）时就会触发一次Refresh，然后Refresh 会把内存中的的数据刷新到操作系统的文件缓存系统中
* 如果我们对搜索的实效性要求不高，可以将Refresh 周期延长，例如30 秒。这样还可以有效地减少段刷新次数，但这同时意味着需要消耗更多的Heap内存

> 加大Flush设置

* Flush 的主要目的是把文件缓存系统中的段持久化到硬盘，当Translog 的数据量达到512MB 或者30 分钟时，会触发一次Flush
* index.translog.flush_threshold_size 参数的默认值是512MB，可以进行自定义修改

> 减少副本的数量

* ES 为了保证集群的可用性，提供了Replicas（副本）支持，然而每个副本也会执行分析、索引及可能的合并过程，所以Replicas 的数量会严重影响写索引的效率
* 如果我们需要大批量进行写入操作，可以先禁止Replica 复制，设置index.number_of_replicas: 0 关闭副本。在写入完成后，Replica 修改回正常的状态

> 内存设置

* 不要超过物理内存的50%：Lucene 的设计目的是把底层OS 里的数据缓存到内存中
* Lucene 的段是分别存储到单个文件中的，这些文件都是不会变化的，所以很利于缓存，同时操作系统也会把这些段文件缓存起来，以便更快的访问
* 如果我们设置的堆内存过大，Lucene 可用的内存将会减少，就会严重影响降低Lucene 的全文本查询性能
* 堆内存的大小最好不要超过32GB：在Java 中，所有对象都分配在堆上，然后有一个Klass Pointer 指针指向它的类元数据