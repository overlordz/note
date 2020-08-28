## 布尔查询

**或的关系**， 关键词 **should** 匹配中 **match** 条件的一种即返回数据

```
# 查询 status 为 0 或 1 的所有数据
{
    "query":{
        "bool":{    
            "should":[
                {
                    "match":{
                        "status":1
                    }
                },
                {
                    "match":{
                        "status":0
                    }
                }
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
                {
                    "match":{
                        "status":1
                    }
                },
                {
                    "match":{
                        "aliasName":"英国联邦"
                    }
                }
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