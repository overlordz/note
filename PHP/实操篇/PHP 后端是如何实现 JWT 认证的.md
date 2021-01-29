#### 什么是 JSON Web Token？

JSON Web Token（JWT）是一个开放标准（RFC 7519），它定义了一种紧凑且自包含的方式，用于在各方之间以 JSON 方式安全地传输信息。由于此信息是经过数字签名的，因此可以被验证和信任。可以使用秘密（使用 HMAC 算法）或使用 RSA 或 ECDSA 的公钥 / 私钥对对 JWT 进行签名。

直白的讲 jwt 就是一种用户认证（区别于 session、cookie）的解决方案。

出现的背景

众所周知，在 jwt 出现之前，我们已经有 session、cookie 来解决用户登录等认证问题，为什么还要 jwt 呢？

这里我们先了解一下 session，cookie。

session

熟悉 session 运行机制的同学都知道，用户的 session 数据以 file 或缓存（redis、memcached）等方式存储在服务器端，客户端浏览器 cookie 中只保存 sessionid。服务器端 session 属于集中存储，数量不大的情况下，没什么问题，当用户数据逐渐增多到一程度，就会给服务端管理和维护带来大的负担。

session 有两个弊端：

1、无法实现跨域。

2、由于 session 数据属于集中管理里，量大的时候服务器性能是个问题。

优点：

1、session 存在服务端，数据相对比较安全。

2、session 集中管理也有好处，就是用户登录、注销服务端可控。

cookie

cookie 也是一种解决网站用户认证的实现方式，用户登录时，服务器会发送包含登录凭据的 Cookie 到用户浏览器客户端，浏览器会将 Cookie 的 key/value 保存用户本地（内存或硬盘），用户再访问网站，浏览器会发送 cookie 信息到服务器端，服务器端接收 cookie 并解析来维护用户的登录状态。

cookie 避免 session 集中管理的问题，但也存在弊端：

1、跨域问题。

2、数据存储在浏览器端，数据容易被窃取及被 csrf 攻击，安全性差。

优点：

1、相对于 session 简单，不用服务端维护用户认证信息。

2、数据持久性。

jwt

jwt 通过 json 传输，php、java、golang 等很多语言支持，通用性比较好，不存在跨域问题。传输数据通过数据签名相对比较安全。客户端与服务端通过 jwt 交互，服务端通过解密 token 信息，来实现用户认证。不需要服务端集中维护 token 信息，便于扩展。当然 jwt 也有其缺点。

缺点：

1、用户无法主动登出，只要 token 在有效期内就有效。这里可以考虑 redis 设置同 token 有效期一直的黑名单解决此问题。

2、token 过了有效期，无法续签问题。可以考虑通过判断旧的 token 什么时候到期，过期的时候刷新 token 续签接口产生新 token 代替旧 token。

jwt 设置有效期

可以设置有效期，加入有效期是为了增加安全性，即 token 被黑客截获，也只能攻击较短时间。设置有效期就会面临 token 续签问题，解决方案如下

通常服务端设置两个 token

*   Access Token：添加到 HTTP 请求的 header 中，进行用户认证，请求接口资源。

*   refresh token：用于当 Access Token 过期后，客户端传递 refresh token 刷新 Access Token 续期接口，获取新的 Access Token 和 refresh token。其有效期比 Access Token 有效期长。

jwt 构成：

*   Header：TOKEN 的类型，就是 JWT，签名的算法，如 HMAC SHA256、HS384

*   Payload：载荷又称为 Claim，携带的信息，比如用户名、过期时间等，一般叫做 Claim

*   Signature：签名，是由 header、payload 和你自己维护的一个 secret 经过加密得来的

jwt 使用

1.  初次登录：用户初次登录，输入用户名密码
2.  密码验证：服务器从数据库取出用户名和密码进行验证
3.  生成 JWT：服务器端验证通过，根据从数据库返回的信息，以及预设规则，生成 JWT
4.  返还 JWT：服务器的 HTTP RESPONSE 中将 JWT 返还
5.  带 JWT 的请求：以后客户端发起请求，HTTP REQUEST
6.  HEADER 中的 Authorizatio 字段都要有值，为 JWT
7.  服务器验证 JWT



#### 代码实现

```php
<?php

class Jwt
{

	//头部
    private static $header = array(
        'alg'  => 'HS256', //生成signature的算法
        'type' => 'JWT'    //类型
    );

	//使用HMAC生成信息摘要时所使用的密钥
    private static $key = '123456';


    /**
     * 获取jwt token
     * @param array $payload jwt载荷   格式如下非必须
     * [
     *  'iss'=>'jwt_admin',  //该JWT的签发者
     *  'iat'=>time(),  //签发时间
     *  'exp'=>time()+7200,  //过期时间
     *  'nbf'=>time()+60,  //该时间之前不接收处理该Token
     *  'sub'=>'www.admin.com',  //面向的用户
     *  'jti'=>md5(uniqid('JWT').time())  //该Token唯一标识
     * ]
     * @return bool|string
     */
    public static function getToken(array $payload)
    {
        if (is_array($payload)) {
            $base64header  = self::base64UrlEncode(json_encode(self::$header, JSON_UNESCAPED_UNICODE));
            $base64payload = self::base64UrlEncode(json_encode($payload, JSON_UNESCAPED_UNICODE));
            $token         = $base64header . '.' . $base64payload . '.' . self::signature($base64header . '.' . $base64payload, self::$key, self::$header['alg']);
            return $token;
        } else {
            return false;
        }
    }


    /**
     * 验证token是否有效,默认验证exp,nbf,iat时间
     * @param string $Token 需要验证的token
     * @return bool|string
     */
    public static function verifyToken(string $Token)
    {
        $tokens = explode('.', $Token);
        if (count($tokens) != 3)
            return false;

        list($base64header, $base64payload, $sign) = $tokens;

		//获取jwt算法
        $base64decodeheader = json_decode(self::base64UrlDecode($base64header), JSON_OBJECT_AS_ARRAY);
        if (empty($base64decodeheader['alg']))
            return false;

		//签名验证
        if (self::signature($base64header . '.' . $base64payload, self::$key, $base64decodeheader['alg']) !== $sign)
            return false;

        $payload = json_decode(self::base64UrlDecode($base64payload), JSON_OBJECT_AS_ARRAY);

		//签发时间大于当前服务器时间验证失败
        if (isset($payload['iat']) && $payload['iat'] > time())
            return false;

		//过期时间小宇当前服务器时间验证失败
        if (isset($payload['exp']) && $payload['exp'] < time())
            return false;

		//该nbf时间之前不接收处理该Token
        if (isset($payload['nbf']) && $payload['nbf'] > time())
            return false;

        return $payload;
    }


    /**
     * base64UrlEncode   https://jwt.io/  中base64UrlEncode编码实现
     * @param string $input 需要编码的字符串
     * @return string
     */
    private static function base64UrlEncode(string $input)
    {
        return str_replace('=', '', strtr(base64_encode($input), '+/', '-_'));
    }

    /**
     * base64UrlEncode  https://jwt.io/  中base64UrlEncode解码实现
     * @param string $input 需要解码的字符串
     * @return bool|string
     */
    private static function base64UrlDecode(string $input)
    {
        $remainder = strlen($input) % 4;
        if ($remainder) {
            $addlen = 4 - $remainder;
            $input  .= str_repeat('=', $addlen);
        }
        return base64_decode(strtr($input, '-_', '+/'));
    }

    /**
     * HMACSHA256签名   https://jwt.io/  中HMACSHA256签名实现
     * @param string $input 为base64UrlEncode(header).".".base64UrlEncode(payload)
     * @param string $key
     * @param string $alg 算法方式
     * @return mixed
     */
    private static function signature(string $input, string $key, string $alg = 'HS256')
    {
        $alg_config = array(
            'HS256' => 'sha256'
        );
        return self::base64UrlEncode(hash_hmac($alg_config[$alg], $input, $key, true));
    }
}

//自己使用测试begin
$payload_test = array('iss' => 'admin', 'iat' => time(), 'exp' => time() + 7200, 'nbf' => time(), 'sub' => 'www.admin.com', 'jti' => md5(uniqid('JWT') . time()));
$token_test = Jwt::getToken($payload_test);
echo "<pre>";
echo $token_test;

//对token进行验证签名
$getPayload_test = Jwt::verifyToken($token_test);
echo "<br><br>";
var_dump($getPayload_test);
echo "<br><br>";
//自己使用时候end
```

