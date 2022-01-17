### MorphOne,MorphMany(都是基于MorphOneOrMany实现)

// 多态一对一、一对多

// hasMorph

```
// exists 语法
SELECT
	* 
FROM
	`image` 
WHERE
    	(
    		(
    			`image`.`imageable_type` = 'brand' 
            AND EXISTS ( SELECT * FROM `brand` WHERE `image`.`imageable_id` = `brand`.`id` AND `brand`.`deleted_at` IS NULL )
		)
	) 
	AND `image`.`deleted_at` IS NULL 
	LIMIT 10 OFFSET 0
 
// in 语法
SELECT
	* 
FROM
	`image` 
WHERE
	(
		(
			`image`.`imageable_type` = 'brand' 
    		AND `image`.`imageable_id` IN ( SELECT `brand`.`id` FROM `brand` WHERE `brand`.`deleted_at` IS NULL )
		)
	) 
	AND `image`.`deleted_at` IS NULL 
	LIMIT 10 OFFSET 0
```

// orHasMorph

// doesntHaveMorph

// orDoesntHaveMorph

// whereHasMorph

// orWhereHasMorph

// whereDoesntHaveMorph

// orWhereDoesntHaveMorph



### HasOne,HasMany(都是基于HasOneOrMany实现)

**// has**

```
// exists 语法
SELECT
	* 
FROM
	`product` 
WHERE
	EXISTS ( SELECT * FROM `product_attr` WHERE `product`.`id` = `product_attr`.`p_id` AND `product_attr`.`deleted_at` IS NULL ) 
	AND `product`.`deleted_at` IS NULL 
	LIMIT 10 OFFSET 0 
// 嵌套关联
SELECT
	* 
FROM
	`product` 
WHERE
	EXISTS (
	SELECT
		* 
	FROM
		`product_attr` 
	WHERE
		`product`.`id` = `product_attr`.`p_id` 
		AND EXISTS ( SELECT * FROM `product_attr_value` WHERE `product_attr`.`id` = `product_attr_value`.`attr_id` AND `product_attr_value`.`deleted_at` IS NULL ) 
		AND `product_attr`.`deleted_at` IS NULL 
	) 
	AND `product`.`deleted_at` IS NULL 
	LIMIT 10 OFFSET 0 
// 自关联
SELECT
	* 
FROM
	`tag_category` 
WHERE
	( `pid` = '0' ) 
	AND EXISTS ( SELECT * FROM `tag_category` AS `laravel_reserved_0` WHERE `tag_category`.`id` = `laravel_reserved_0`.`pid` AND `laravel_reserved_0`.`deleted_at` IS NULL ) 
	AND `tag_category`.`deleted_at` IS NULL
 
// in 语法 
SELECT
	* 
FROM
	`product` 
WHERE
	`product`.`id` IN ( SELECT `product_attr`.`p_id` FROM `product_attr` WHERE `product_attr`.`deleted_at` IS NULL ) 
	AND `product`.`deleted_at` IS NULL 
	LIMIT 10 OFFSET 0
// 嵌套关联
SELECT
	* 
FROM
	`product` 
WHERE
	`product`.`id` IN (
	SELECT
		`product_attr`.`p_id` 
	FROM
		`product_attr` 
	WHERE
		`product_attr`.`id` IN ( SELECT `product_attr_value`.`attr_id` FROM `product_attr_value` WHERE `product_attr_value`.`deleted_at` IS NULL ) 
		AND `product_attr`.`deleted_at` IS NULL 
	) 
	AND `product`.`deleted_at` IS NULL 
	LIMIT 10 OFFSET 0
 // 自关联
 SELECT
	* 
FROM
	`tag_category` 
WHERE
	( `pid` = '0' ) 
	AND `tag_category`.`id` IN ( SELECT `tag_category`.`pid` FROM `tag_category` WHERE `tag_category`.`deleted_at` IS NULL ) 
	AND `tag_category`.`deleted_at` IS NULL
```

**// orHas**

```
// exists
SELECT * FROM `product` WHERE (( `name` LIKE '%拌饭酱%' ) 
OR EXISTS ( SELECT * FROM `product_sku` WHERE `product`.`id` = `product_sku`.`p_id` AND `product_sku`.`deleted_at` IS NULL )) 
AND `product`.`deleted_at` IS NULL 
LIMIT 10 OFFSET 0

// in
SELECT * FROM `product` WHERE (( `name` LIKE '%拌饭酱%' ) 
OR `product`.`id` IN ( SELECT `product_sku`.`p_id` FROM `product_sku` WHERE `product_sku`.`deleted_at` IS NULL )) 
AND `product`.`deleted_at` IS NULL 
LIMIT 10 OFFSET 0
```

**// doesntHave**

```
// exists
SELECT
	* 
FROM
	`product` 
WHERE
	NOT EXISTS ( SELECT * FROM `product_sku` WHERE `product`.`id` = `product_sku`.`p_id` AND `product_sku`.`deleted_at` IS NULL ) 
	AND `product`.`deleted_at` IS NULL 
	LIMIT 10 OFFSET 0
 
// in
SELECT
	* 
FROM
	`product` 
WHERE
	`product`.`id` NOT IN ( SELECT `product_sku`.`p_id` FROM `product_sku` WHERE `product_sku`.`deleted_at` IS NULL ) 
	AND `product`.`deleted_at` IS NULL 
	LIMIT 10 OFFSET 0 
```

**// orDoesntHave**

```
// exists
SELECT * FROM `product` WHERE (( `name` LIKE '%拌饭酱%' ) 
OR NOT EXISTS ( SELECT * FROM `product_sku` WHERE `product`.`id` = `product_sku`.`p_id` AND `product_sku`.`deleted_at` IS NULL )) 
AND `product`.`deleted_at` IS NULL 
LIMIT 10 OFFSET 0

// in
SELECT * FROM `product` WHERE (( `name` LIKE '%拌饭酱%' ) 
OR `product`.`id` NOT IN ( SELECT `product_sku`.`p_id` FROM `product_sku` WHERE `product_sku`.`deleted_at` IS NULL )) 
AND `product`.`deleted_at` IS NULL 
LIMIT 10 OFFSET 0
```

**// whereHas**

```
// exists
SELECT
	* 
FROM
	`product` 
WHERE
	EXISTS ( SELECT * FROM `product_sku` WHERE `product`.`id` = `product_sku`.`p_id` AND `sales` > '10' AND `product_sku`.`deleted_at` IS NULL ) 
	AND `product`.`deleted_at` IS NULL 
	LIMIT 10 OFFSET 0
 
// in
SELECT
	* 
FROM
	`product` 
WHERE
	`product`.`id` IN ( SELECT `product_sku`.`p_id` FROM `product_sku` WHERE `sales` > '10' AND `product_sku`.`deleted_at` IS NULL ) 
	AND `product`.`deleted_at` IS NULL 
	LIMIT 10 OFFSET 0 
```

**// orWhereHas**

```
// exists
SELECT * FROM `product` WHERE (( `name` LIKE '%拌饭酱%' ) 
OR EXISTS ( SELECT * FROM `product_sku` WHERE `product`.`id` = `product_sku`.`p_id` AND `sales` > '10' AND `product_sku`.`deleted_at` IS NULL )) 
AND `product`.`deleted_at` IS NULL 
LIMIT 10 OFFSET 0

// in
SELECT * FROM `product` WHERE (( `name` LIKE '%拌饭酱%' ) 
OR `product`.`id` IN ( SELECT `product_sku`.`p_id` FROM `product_sku` WHERE `sales` > '10' AND `product_sku`.`deleted_at` IS NULL )) 
AND `product`.`deleted_at` IS NULL 
LIMIT 10 OFFSET 0
```



**// whereDoesntHave**

```
// exists
SELECT
	* 
FROM
	`product` 
WHERE
	NOT EXISTS ( SELECT * FROM `product_sku` WHERE `product`.`id` = `product_sku`.`p_id` AND `sales` > '10' AND `product_sku`.`deleted_at` IS NULL ) 
	AND `product`.`deleted_at` IS NULL 
	LIMIT 10 OFFSET 0
 
// in
SELECT
	* 
FROM
	`product` 
WHERE
	`product`.`id` NOT IN ( SELECT `product_sku`.`p_id` FROM `product_sku` WHERE `sales` > '10' AND `product_sku`.`deleted_at` IS NULL ) 
	AND `product`.`deleted_at` IS NULL 
	LIMIT 10 OFFSET 0
```

**// orWhereDoesntHave**

```
// exists
SELECT * FROM `product` WHERE (( `name` LIKE '%拌饭酱%' ) 
OR NOT EXISTS ( SELECT * FROM `product_sku` WHERE `product`.`id` = `product_sku`.`p_id` AND `sales` > '10' AND `product_sku`.`deleted_at` IS NULL )) 
AND `product`.`deleted_at` IS NULL 
LIMIT 10 OFFSET 0

// in
SELECT * FROM `product` WHERE (( `name` LIKE '%拌饭酱%' ) 
OR `product`.`id` NOT IN ( SELECT `product_sku`.`p_id` FROM `product_sku` WHERE `sales` > '10' AND `product_sku`.`deleted_at` IS NULL )) 
AND `product`.`deleted_at` IS NULL 
LIMIT 10 OFFSET 0
```



### HasOneThrough,HasManyThrough

// has

```
// exists 语法
SELECT
	* 
FROM
	`product` 
WHERE
	EXISTS (
	SELECT
		* 
	FROM
		`product_attr_value`
		INNER JOIN `product_attr` ON `product_attr`.`id` = `product_attr_value`.`attr_id` 
	WHERE
		`product`.`id` = `product_attr`.`p_id` 
		AND `product_attr_value`.`deleted_at` IS NULL 
		AND `product_attr`.`deleted_at` IS NULL 
	) 
	AND `product`.`deleted_at` IS NULL 
	LIMIT 10 OFFSET 0

// in 语法 
SELECT
	* 
FROM
	`product` 
WHERE
	`product`.`id` IN (
	SELECT
		`product_attr`.`p_id` 
	FROM
		`product_attr_value`
		INNER JOIN `product_attr` ON `product_attr`.`id` = `product_attr_value`.`attr_id` 
	WHERE
		`product_attr_value`.`deleted_at` IS NULL 
		AND `product_attr`.`deleted_at` IS NULL 
	) 
	AND `product`.`deleted_at` IS NULL 
	LIMIT 10 OFFSET 0
```

// orHas

// doesntHave

// orDoesntHave

// whereHas

// orWhereHas

// whereDoesntHave

// orWhereDoesntHave



### BelongsTo,MorphTo

// has

```
// exists 语法
SELECT
	* 
FROM
	`product` 
WHERE
	EXISTS ( SELECT * FROM `tag_type` WHERE `product`.`type_id` = `tag_type`.`id` AND `tag_type`.`deleted_at` IS NULL ) 
	AND `product`.`deleted_at` IS NULL 
	LIMIT 10 OFFSET 0 

// in 语法
SELECT
	* 
FROM
	`product` 
WHERE
	`product`.`type_id` IN ( SELECT `tag_type`.`id` FROM `tag_type` WHERE `tag_type`.`deleted_at` IS NULL ) 
	AND `product`.`deleted_at` IS NULL 
	LIMIT 10 OFFSET 0
```

// orHas

// doesntHave

// orDoesntHave

// whereHas

// orWhereHas

// whereDoesntHave

// orWhereDoesntHave



### BelongsToMany

// has

```
// exists 语法
SELECT
	* 
FROM
	`product` 
WHERE
	EXISTS (
	SELECT
		* 
	FROM
		`tag_category`
		INNER JOIN `relation_product_tag_category` ON `tag_category`.`id` = `relation_product_tag_category`.`cate_id` 
	WHERE
		`product`.`id` = `relation_product_tag_category`.`product_id` 
		AND `tag_category`.`deleted_at` IS NULL 
	) 
	AND `product`.`deleted_at` IS NULL 
	LIMIT 10 OFFSET 0 

// in 语法
SELECT
	* 
FROM
	`product` 
WHERE
	`product`.`id` IN (
	SELECT `relation_product_tag_category`.`product_id`
	FROM 
		`tag_category`
		INNER JOIN `relation_product_tag_category` ON `tag_category`.`id` = `relation_product_tag_category`.`cate_id`
	WHERE
		`tag_category`.`deleted_at` IS NULL
	) 
	AND `product`.`deleted_at` IS NULL 
	LIMIT 10 OFFSET 0
```

// orHas

// doesntHave

// orDoesntHave

// whereHas

// orWhereHas

// whereDoesntHave

// orWhereDoesntHave



### MorphToMany

// has

```
// exists 语法
SELECT
    * 
FROM
    	`product` 
WHERE
    	EXISTS (
    	SELECT
    		* 
	FROM
		`image`
		INNER JOIN `able_image` ON `image`.`id` = `able_image`.`image_id` 
	WHERE
		`product`.`id` = `able_image`.`imageable_id` 
		AND `able_image`.`imageable_type` = 'product' 
		AND `image`.`deleted_at` IS NULL 
	) 
    AND `product`.`deleted_at` IS NULL 
    LIMIT 10 OFFSET 0

// in 语法
SELECT
	* 
FROM
	`product` 
WHERE
	`product`.`id` IN (
	SELECT
		`able_image`.`imageable_id` 
	FROM
		`image`
		INNER JOIN `able_image` ON `image`.`id` = `able_image`.`image_id` 
	WHERE
		`able_image`.`imageable_type` = 'product' 
		AND `image`.`deleted_at` IS NULL 
	) 
	AND `product`.`deleted_at` IS NULL 
	LIMIT 10 OFFSET 0
```

// orHas

// doesntHave

// orDoesntHave

// whereHas

// orWhereHas

// whereDoesntHave

// orWhereDoesntHave