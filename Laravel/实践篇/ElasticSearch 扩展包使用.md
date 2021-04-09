##### 安装

> composer require elasticsearch/elasticsearch 

##### 官方文档

> https://www.elastic.co/guide/en/elasticsearch/client/php-api/current/search_operations.html



##### 示例

```PHP
<?php

use Elasticsearch\ClientBuilder;


// 获取单条数据
public function index(){
     $hosts = [
            'http://192.168.1.201:9200'
        ];
     $client = ClientBuilder::create()
            ->setHosts($hosts)
            ->build();
    $params = [
        'index' => env('HELPER_REPAIR_CASE_INDEX'),
        'type'  => env('HELPER_REPAIR_CASE_TYPES'),
        'id'    => '1'
    ];

    $response = $client->get($params);
    print_r($response);exit;
}

// 条件搜索
public funciton search(){
     	$hosts    = [
            env('ELASTICSEARCH_HOST')
        ];
        $client   = ClientBuilder::create()->setHosts($hosts)->build();
        $where   = [
            'index'   => env('HELPER_REPAIR_CASE_INDEX'),
            'type'    => env('HELPER_REPAIR_CASE_TYPES'),
            'size'    => 1000,
            '_source' => ['id', 'title'],
            'body'    => [
                'query' => [
                    'bool' => [
                        'must' => [
                            ['match' => ['car_brand' => $carBrand]],
                            ['match' => ['car_line' => $carLine]],
                            ['exists' => ['field' => 'gearbox_oil']],
                            ['exists' => ['field' => 'antifreeze']]
                        ]
                    ]
                ],
                'sort'  => ['id' => ['order' => 'asc']]
            ]
        ];
    	// 条件使用json_encode($where) 查看是否跟我们旧项目 string查询一致
	    $response = $client->search($where);
   	 
}

// 直接用原生json string 搜索【旧项目做法】
      	$json = '{
            "query" : {
                "match" : {
                    "testField" : "abc"
                }
            }
        }';

        $params = [
            'index' => 'my_index',
            'body'  => $json
        ];

        $results = $client->search($params);  

// 删除文档
    $params = [
        'index' => env('HELPER_REPAIR_CASE_INDEX'),
        'type'  => env('HELPER_REPAIR_CASE_TYPES'),
        'id'    => 'my_id'
    ];
    $response = $client->delete($params);
    print_r($response);

```

