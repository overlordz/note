> 原文地址 [www.cnblogs.com](https://www.cnblogs.com/x-x-j/p/7921164.html)

### with()

#### with()方法是用作 “渴求式加载” 的，那主要意味着，laravel 将会伴随着主要模型预加载出确切的的关联关系。这就对那些如果你想加在一个模型的所有关联关系非常有帮助。因为 “渴求式加载” 缓解了 1+N 的查询问题，仅需 1+1 次查询就能解决问题，对查询速度有了质的提升。

#### 例如：

user > hasMany > post

```
1 $users = User::with('posts')->get();
2 foreach($users as $user){
3     $users->posts; // posts已经被加载了，没有增加DB查询
4 }
```



```
Category > hasMany > good

查询某个分类下的商品方法
public function goods($categoryId)
{
     $category = Category::find($categoryId);
     $goods    = $category->goods;
     return $goods;
}


从同一个接口返回分类和属于该分类的商品，此刻就用到了with
public function category($categoryId)
{
    $category = Category::with('goods')->find($categoryId);
    return $category;
}


查询一个分类下已经上架的商品，用到了with另一方法
public function category($categoryId)
{
    $category = Category::with(['goods'=>function($query){
    	  $query->where('is_sale', 1);
	}])->find($categoryId);
    return $category;
}
```



### has()

#### has() 方法是基于关联关系去过滤模型的查询结果，所以它的作用和 where 条件非常相似。如果你只使用 has('post'), 这表示你只想得到这个模型，这个模型的至少存在一个 post 的关联关系。

#### 例如：

user > hasMany > post

```
1 //User至少有一条post的关联关系
2 $users = User::has('post')->get();
```



#### 你还可以使用”.“来构造嵌套 has 语句。

#### 例如：

user > hasMany > post

```
1 $user = User::has('post.votes', ‘>’, '3')->get();
```



### whereHas()

#### whereHas() 方法的原理基本和 has() 方法相同，但是他允许你自己添加对这个模型的过滤条件。

#### 例如：

user > hasMany > post


```
1 $users = User::whereHas('posts', function($q){
2     $q->where('created_at', '>=', '2017-11-29');
3 })->get();
4 // 只返回用户的post记录在2017年11月29之后的数据
```
