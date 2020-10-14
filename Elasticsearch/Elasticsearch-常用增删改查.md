> ElasticSearch 版本为 "5.6.4"

## 数据源

假设请求地址：http://localhost:9200/search_index/type/_search?pretty&size=20&from=0

```
{
    "took": 1,
    "timed_out": false,
    "_shards": {
        "total": 5,
        "successful": 5,
        "skipped": 0,
        "failed": 0
    },
    "hits": {
        "total": 258,
        "max_score": 1.0,
        "hits": [
            {
                "_index": "approve_dev",
                "_type": "approve_type",
                "_id": "b81f3e0881154eae2181d0e47095ec96",
                "_score": 1.0,
                "_source": {
                    "applyId": "b81f3e0881154eae2181d0e47095ec96",
                    "name": "英国",
                    "aliasName": "英国联邦",
                    "isStatus": "3",
                    "addTime": "2017-04-27 14:39:50",
                    "address": "广东省广州市白云区"
                }
            },
            {
                "_index": "approve_dev",
                "_type": "approve_type",
                "_id": "e3e98b7e86a155c85c4d448bbb567cde",
                "_score": 1.0,
                "_source": {
                    "applyId": "e3e98b7e86a155c85c4d448bbb567cde",
                    "name": "美国",
                    "aliasName": "美国联邦",
                    "isStatus": "1",
                    "addTime": "2017-04-27 18:59:23",
                    "address": "广东省广州市天河区"
                }
            },
            {
                "_index": "approve_dev",
                "_type": "approve_type",
                "_id": "fc7eecb15c1928b2f964f96f47d42739",
                "_score": 1.0,
                "_source": {
                    "applyId": "fc7eecb15c1928b2f964f96f47d42739",
                    "name": "中国",
                    "aliasName": "中国特工",
                    "isStatus": "2",
                    "addTime": "2017-04-27 20:54:02",
                    "address": "广东省佛山市南海区"
                }
            }
        ]
    }
}
```



## 匹配查询

#### #精准查询

`term` 主要用于精确匹配哪些值，比如数字，日期，布尔值。一个 `term` 查询结构：

```
{
    "query":{
        "term": { "applyId": "b81f3e0881154eae2181d0e47095ec96" } 
    }
}
```

与其类似的 `terms` 主要用来匹配多个条件的过滤查询。比如

```
{
    "query":{
        "terms":{
            "applyId":[
                "b81f3e0881154eae2181d0e47095ec96",
                "e3e98b7e86a155c85c4d448bbb567cde"
            ]
        }
    }
}
```



`match` & `match_phrase`

注意事项：假设 `match` 查看的字段 `mapping`  为 text 类型，那么搜索会自动分词，导致不能只查找 要查询的内容

那么想准确匹配内容则用 `match_phrase`

```
{
    "query":{
        "match":{
            "aliasName":"特工"
        }
    }
}
# 要求更精准搜索
{
    "query":{
        "`match_phrase`":{
            "aliasName":"中国特工"
        }
    }
}
```



#### #多个字段匹配查询

对 name 和 aliasName 两个字段查询 “国”，可以看到含有 “国” 的 name  和 aliasName  都查出来了（ps: 创建索引时 name  是keyword只能精确查找，不会分词）

```
# 方法一：
{
    "query":{
        "`multi_match`":{
        	"query":"国",
            "fields":["name","aliasName"]
        }
    }
}

# 方法二： 
{
    "query":{
        "`query_string`":{
        	"query":"国",
            "fields":["name","aliasName"]
        }
    }
}

# query_string 延伸使用 指定 name 和 aliasName 字段，查询 国 或 中
{
    "query":{
        "`query_string`":{
        	"query":"国 OR 中",
            "fields":["name","aliasName"]
        }
    }
}
```



#### #非空查询

`exists & missing` 用于查找记录中是否存在或者没有某个指定的字段。

注意官方解释：空的字符串也是一种数据类型（**“ ”** 或 **“null”**），而exists只能过滤掉为 **null** 和 **不存在** 的字段，

```
{
	"query":{
  		"exists": {"field": "aliasName"}
	}
}
```

`must_not` 也可以查看非空的字符串，不过其值要求是 “ ” 或 null

```
{
	"query":{
		"must_not":{"match":{"aliasName":""}}
	}
}
```



## 布尔查询

**或的关系**， 关键词 **should** 匹配中 **match** 条件的一种即返回数据

```
# 查询 status 为 0 或 1 的所有数据
{
    "query":{
        "bool":{    
            "should":[
                { "match":{ "isStatus":1 } },
                { "match":{ "isStatus":0 } }
            ]
        }
    }
}
```



**与的关系**，关键词 **must**，匹配中 match 所有条件即返回数据

```
# 查询 status 为1 且 aliasName 为 英国联邦 的所有数据
{
    "query":{
        "bool":{    
            "must":[
                { "match":{ "isStatus":1 } },
                { "match":{ "aliasName":"英国联邦" } }
            ]
        }
    }
}
```



## 区间查询

| 字符 | 解释       |
| ---- | ---------- |
| gt   | 大于       |
| gte  | 大于或等于 |
| lt   | 小于       |
| lte  | 小于或等于 |

#### 查询日期

```
# 查询创建时间是 2020年1月1号到现在 的所有数据
{
    "query":{
        "range":{    
           "create_date":{
           		"gt":"2020-01-01"
           		"lte":"now"
           }
        }
    }
}
```





参考资料：

- https://www.pianshen.com/article/1530756399/
- http://www.mianshigee.com/tutorial/elasticsearch/30_existsmissing