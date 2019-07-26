##### 1、创建一个临时内存表, 做数据插入的时候会比较快些（存储引擎为Memory）

```
DROP TABLE IF EXISTS `vote_record_memory`;
CREATE TABLE `vote_record_memory` (
    `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
    `user_id` varchar(20) NOT NULL DEFAULT '',
    `vote_num` int(10) unsigned NOT NULL DEFAULT '0',
    `group_id` int(10) unsigned NOT NULL DEFAULT '0',
    `status` tinyint(2) unsigned NOT NULL DEFAULT '1',
    `create_time` datetime NOT NULL DEFAULT '0000-00-00 00:00:00',
    PRIMARY KEY (`id`),
    KEY `index_user_id` (`user_id`) USING HASH
) ENGINE=Memory AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
```



##### 2、创建一个普通表，用作模拟大数据的测试用例

```
DROP TABLE IF EXISTS `vote_record`;
CREATE TABLE `vote_record` (
    `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
    `user_id` varchar(20) NOT NULL DEFAULT '' COMMENT '用户Id',
    `vote_num` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '投票数',
    `group_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '用户组id 0-未激活用户 1-普通用户 2-vip用户 3-管理员用户',
    `status` tinyint(2) unsigned NOT NULL DEFAULT '1' COMMENT '状态 1-正常 2-已删除',
    `create_time` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '创建时间',
    PRIMARY KEY (`id`),
    KEY `index_user_id` (`user_id`) USING HASH COMMENT '用户ID哈希索引'
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='投票记录表';
```



##### 3、为了数据的随机性和真实性，我们需要创建一个可生成长度为n的随机字符串的函数（自定义函数）

```
CREATE FUNCTION `rand_string` (n INT) RETURNS VARCHAR(255) CHARSET 'utf8'
BEGIN 
    DECLARE char_str varchar(100) DEFAULT 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789';
    DECLARE return_str varchar(255) DEFAULT '';
    DECLARE i INT DEFAULT 0;
    WHILE i < n DO
        SET return_str = concat(return_str, substring(char_str, FLOOR(1 + RAND()*62), 1));
        SET i = i+1;
    END WHILE;
    RETURN return_str;
END
```



##### 4、为了操作方便，我们再创建一个插入数据的存储过程（存储过程）

```
CREATE PROCEDURE `add_vote_record_memory`(IN n INT)
BEGIN
    DECLARE i INT DEFAULT 1;
    DECLARE vote_num INT DEFAULT 0;
    DECLARE group_id INT DEFAULT 0;
    DECLARE status TINYINT DEFAULT 1;
    WHILE i < n DO
        SET vote_num = FLOOR(1 + RAND() * 10000);
        SET group_id = FLOOR(0 + RAND()*3);
        SET status = FLOOR(1 + RAND()*2);
        INSERT INTO `vote_record_memory` VALUES (NULL, rand_string(20), vote_num, group_id, status, NOW());
        SET i = i + 1;
    END WHILE;
END
```



##### 5、开始执行存储过程，等待生成数据(1000W条生成大约需要400分钟)

```
//调用存储过程 生成1000W条数据
CALL add_vote_record_memory(10000000);
```



##### 6、查询内存表已生成记录(为了下步测试，目前仅生成了105645条)

```
SELECT count(*) FROM `vote_record_memory`;
```



##### 7、把数据从内存表插入到普通表中(10w条数据13s就插入完了)

```
INSERT INTO vote_record SELECT * FROM `vote_record_memory`;
```



##### 8、查询普通表已的生成记录

```
SELECT count(*) FROM `vote_record`;
```







**本篇内容转载自：<https://www.phpnote.cc/mysql-make-large-data>**