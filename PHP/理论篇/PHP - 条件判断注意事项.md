### PHP版本：7.*+

| 类型    | null      | false     | 空串      | 0         | '0'       | 空数组    | 空对象    | 不存在变量         |
| ------- | --------- | --------- | --------- | --------- | --------- | --------- | --------- | ------------------ |
| isset   | ==false== | true      | true      | true      | true      | true      | true      | ==false==          |
| empty   | true      | true      | true      | true      | true      | true      | ==false== | true               |
| is_null | true      | ==false== | ==false== | ==false== | ==false== | ==false== | ==false== | true(报notice错误) |



#### 标注：null == 0 == false == ''

| 等值[==] | null      | false | 空串      | 0    | '0'       |
| -------- | --------- | ----- | --------- | ---- | --------- |
| null     | -         | true  | true      | true | ==false== |
| false    | true      | -     | true      | true | true      |
| 空串     | true      | true  | -         | true | ==false== |
| 0        | true      | true  | true      | -    | true      |
| '0'      | ==false== | true  | ==false== | true | -         |


| 全等值[===] | null      | false     | 空串      | 0         | '0'       |
| ----------- | --------- | --------- | --------- | --------- | --------- |
| null        | -         | ==false== | ==false== | ==false== | ==false== |
| false       | ==false== | -         | ==false== | ==false== | ==false== |
| 空串        | ==false== | ==false== | -         | ==false== | ==false== |
| 0           | ==false== | ==false== | ==false== | -         | ==false== |
| '0'         | ==false== | ==false== | ==false== | ==false== | -         |