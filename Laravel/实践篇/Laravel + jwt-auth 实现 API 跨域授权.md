> 原文地址 [www.cnblogs.com](https://www.cnblogs.com/caibaotimes/articles/13608243.html)

在开发 Api 时，处理客户端请求之前，需要对用户进行身份认证，Laravel 框架默认为我们提供了一套用户认证体系，在进行 web 开发时，几乎不用添加修改任何代码，可直接使用，**但在进行 api 开发时，需要我们自己去实现，并且 Laravel 框架默认提供的身份认证不是 jwt 的，需要在数据库中增加 api_token 字段，记录用户认证 token 并进行身份校验**，**如果需要使用 jwt，无需添加字段，需要借助三方库来实现。**

### Token 认证原理[#](#4199263026)

1.  客户端发送认证信息 (一般就是用户名 / 密码), 向服务器发送请求
2.  服务器验证客户端的认证信息，验证成功之后，服务器向客户端返回一个 加密的 token (一般情况下就是一个字符串)
3.  客户端存储 (cookie, session, app 中都可以存储) 这个 token, 在之后每次向服务器发送请求时，都携带上这个 token
4.  服务器验证这个 token 的合法性，只要验证通过，服务器就认为该请求是一个合法的请求

### JWT 概述[#](#347736699)

token 只是一种思路，一种解决用户授权问题的思考方式，基于这种思路，针对不同的场景可以有很多种的实现。而在众多的实现中，JWT (JSON Web Token) 的实现最为流行.

JWT 这个标准提供了一系列如何创建具体 token 的方法，这些缘故方法和规范可以让我们创建 token 的过程变得更加合理和效率.

比如，传统的做法中，服务器会保存生成的 token, 当客户端发送来 token 时，与服务器的进行比对，**但是 jwt 的不需要在服务器保存任何 token, 而是使用一套加密 / 解密算法 和 一个密钥 来对用户发来的 token 进行解密，解密成功后就可以得到这个用户的信息.**

**这样的做法同时也增加了多服务器时的扩展性，在传统的 token 验证中，一旦用户发来 token, 那么必须要先找到存储这个 token 的服务器是哪台服务器，然后由那一台服务器进行验证用户身份。而 jwt 的存在，只要每一台服务器都知道解密密钥，那么每一台服务器都可以拥有验证用户身份的能力.**

这样一来，服务器就不再保存任何用户授权的信息了，也就解决了 session 曾出现的问题.

> **JWT 标准的 Token 有三个部分：header、payload、signature**
>
> **客户端收到这个 Token 以后把它存储下来，下次向服务端发送请求的时候就带着这个 Token 。服务端收到这个 Token ，然后进行验证，通过以后就会返回给客户端想要的资源。验证的过程就是**  
> **根据传过来的 token 再生成一下第三部分 Signature，然后两个比对一下，一致就验证通过。**

[![](https://img2020.cnblogs.com/blog/2137920/202105/2137920-20210510193459942-2031178555.jpg)](https://img2020.cnblogs.com/blog/2137920/202105/2137920-20210510193459942-2031178555.jpg)

一、安装及基础配置[#](#1572933034)
-------------------------

### 1. 使用 composer 安装[#](#3660204644)

```
# 建议使用1.0以上版本
composer require tymon/jwt-auth 1.*@rc


```

### 2. 进行一些配置[#](#3545617052)

这里值得注意的是，有些文档会说要添加 Tymon\JWTAuth\Providers\LaravelServiceProvider::class ，这只在 Laravel 5.4 及以下版本是必要的，更新的 Laravel 版本无需添加。

还有一些文档说要添加 Tymon\JWTAuth\Providers\JWTAuthServiceProvider 这是很久以前的 JWT 版本的（大概 0.5.3 以前的版本）。

#### 2.1 发布配置文件

```
# 这条命令会在 config 下增加一个 jwt.php 的配置文件
php artisan vendor:publish --provider="Tymon\JWTAuth\Providers\LaravelServiceProvider"


```

#### 2.2 生成加密密钥

```
# 这条命令会在 .env 文件下生成一个加密密钥，如：JWT_SECRET=foobar
php artisan jwt:secret


```

#### 2.3 更新你的模型

如果你使用默认的 User 表来生成 token，你需要在该模型下增加一段代码

```
<?php

namespace App;

use Tymon\JWTAuth\Contracts\JWTSubject;
use Illuminate\Notifications\Notifiable;
use Illuminate\Foundation\Auth\User as Authenticatable;

class User extends Authenticatable implements JWTSubject    # 这里别忘了加
{
    use Notifiable;

    // Rest omitted for brevity

    /**
     * Get the identifier that will be stored in the subject claim of the JWT.
     *
     * @return mixed
     */
    public function getJWTIdentifier()
    {
        return $this->getKey();
    }

    /**
     * Return a key value array, containing any custom claims to be added to the JWT.
     *
     * @return array
     */
    public function getJWTCustomClaims()
    {
        return [];
    }
}


```

#### 2.4 注册两个 Facade

这两个 Facade 并不是必须的，但是使用它们会给你的代码编写带来一点便利。

**config/app.php**

```
'aliases' => [
        ...
        // 添加以下两行
        'JWTAuth' => 'Tymon\JWTAuth\Facades\JWTAuth',
        'JWTFactory' => 'Tymon\JWTAuth\Facades\JWTFactory',
],


```

**如果你不使用这两个 Facade，你可以使用辅助函数 auth ()**

auth () 是一个辅助函数，返回一个 guard，暂时可以看成 Auth Facade。

```
// 如果你不用 Facade，你可以这么写
auth('api')->refresh();
// 用 JWTAuth Facade
JWTAuth::parseToken()->refresh();


```

两个 Facede 常用可使用方法，可以看文章后面的附录。

#### 2.5 修改 auth.php

**config/auth.php**

```
'guards' => [
    'web' => [
        'driver' => 'session',
        'provider' => 'users',
    ],

    'api' => [
        'driver' => 'jwt',        // 原来是 token 改成jwt
        'provider' => 'users',
    ],
],


```

#### 2.6 注册一些路由

注意：在 Laravel 下，route/api.php 中的路由默认都有前缀 api 。

```
Route::group([

    'prefix' => 'auth'

], function ($router) {

    Route::post('login', 'AuthController@login');
    Route::post('logout', 'AuthController@logout');
    Route::post('refresh', 'AuthController@refresh');
    Route::post('me', 'AuthController@me');

});


```

#### 2.7 创建 token 控制器

```
php artisan make:controller AuthController


```

AuthController

值得注意的是 Laravel 这要用 auth('api')

```
<?php

namespace App\Http\Controllers;

use Illuminate\Support\Facades\Auth;
use App\Http\Controllers\Controller;

class AuthController extends Controller
{
    /**
     * Create a new AuthController instance.
     * 要求附带email和password（数据来源users表）
     * 
     * @return void
     */
    public function __construct()
    {
          // 这里额外注意了：官方文档样例中只除外了『login』
          // 这样的结果是，token 只能在有效期以内进行刷新，过期无法刷新
          // 如果把 refresh 也放进去，token 即使过期但仍在刷新期以内也可刷新
          // 不过刷新一次作废
        $this->middleware('auth:api', ['except' => ['login']]);
          // 另外关于上面的中间件，官方文档写的是『auth:api』
          // 但是我推荐用 『jwt.auth』，效果是一样的，但是有更加丰富的报错信息返回
    }

    /**
     * Get a JWT via given credentials.
     *
     * @return \Illuminate\Http\JsonResponse
     */
    public function login()
    {
        $credentials = request(['email', 'password']);

        if (! $token = auth('api')->attempt($credentials)) {
            return response()->json(['error' => 'Unauthorized'], 401);
        }

        return $this->respondWithToken($token);
    }

    /**
     * Get the authenticated User.
     *
     * @return \Illuminate\Http\JsonResponse
     */
    public function me()
    {
        return response()->json(auth('api')->user());
    }

    /**
     * Log the user out (Invalidate the token).
     *
     * @return \Illuminate\Http\JsonResponse
     */
    public function logout()
    {
        auth('api')->logout();

        return response()->json(['message' => 'Successfully logged out']);
    }

    /**
     * Refresh a token.
     * 刷新token，如果开启黑名单，以前的token便会失效。
     * 值得注意的是用上面的getToken再获取一次Token并不算做刷新，两次获得的Token是并行的，即两个都可用。
     * @return \Illuminate\Http\JsonResponse
     */
    public function refresh()
    {
        return $this->respondWithToken(auth('api')->refresh());
    }

    /**
     * Get the token array structure.
     *
     * @param  string $token
     *
     * @return \Illuminate\Http\JsonResponse
     */
    protected function respondWithToken($token)
    {
        return response()->json([
            'access_token' => $token,
            'token_type' => 'bearer',
            'expires_in' => auth('api')->factory()->getTTL() * 60
        ]);
    }
}


```

### 关于中间件[#](#2462888749)

1.0 版本以上的 jwt-auth，中间件在服务提供者中已经定义了，所以不需要额外写，按上面来即可。

下面是可用的中间件，第一二个功能一样，但是第二个不会抛出错误，第三四个功能一样，没什么区别。

`tymon\jwt-auth\src\Providers\AbstractServiceProvider.php`

```
protected $middlewareAliases = [
    'jwt.auth' => Authenticate::class,
    'jwt.check' => Check::class,
    'jwt.refresh' => RefreshToken::class,
    'jwt.renew' => AuthenticateAndRenew::class,
];


```

### 自定义认证中间件[#](#39878941)

先来说明一下我想要达成的效果，我希望用户提供账号密码前来登录。如果登录成功，那么我会给前端颁发一个 access _token ，设置在 header 中以请求需要用户认证的路由。

同时我希望如果用户的令牌如果过期了，可以暂时通过此次请求，并在此次请求中刷新该用户的 access _token，最后在响应头中将新的 access _token 返回给前端，这样子可以无痛的刷新 access _token ，用户可以获得一个很良好的体验，所以开始动手写代码。

执行如下命令以新建一个中间件：

```
php artisan make:middleware RefreshToken


```

中间件代码如下：

RefreshToken.php

```
<?php

namespace App\Http\Middleware;

use Auth;
use Closure;
use Tymon\JWTAuth\Exceptions\JWTException;
use Tymon\JWTAuth\Http\Middleware\BaseMiddleware;
use Tymon\JWTAuth\Exceptions\TokenExpiredException;
use Symfony\Component\HttpKernel\Exception\UnauthorizedHttpException;

// 注意，我们要继承的是 jwt 的 BaseMiddleware
class RefreshToken extends BaseMiddleware
{
    /**
     * Handle an incoming request.
     *
     * @param  \Illuminate\Http\Request $request
     * @param  \Closure $next
     *
     * @throws \Symfony\Component\HttpKernel\Exception\UnauthorizedHttpException
     *
     * @return mixed
     */
    public function handle($request, Closure $next)
    {
        // 检查此次请求中是否带有 token，如果没有则抛出异常。 
        $this->checkForToken($request);

       // 使用 try 包裹，以捕捉 token 过期所抛出的 TokenExpiredException  异常
        try {
            // 检测用户的登录状态，如果正常则通过
            if ($this->auth->parseToken()->authenticate()) {
                return $next($request);
            }
            throw new UnauthorizedHttpException('jwt-auth', '未登录');
        } catch (TokenExpiredException $exception) {
          // 此处捕获到了 token 过期所抛出的 TokenExpiredException 异常，我们在这里需要做的是刷新该用户的 token 并将它添加到响应头中
            try {
                // 刷新用户的 token
                $token = $this->auth->parseToken()->refresh();
               // 使用一次性登录以保证此次请求的成功
                Auth::guard('api')->onceUsingId($this->auth->manager()->getPayloadFactory()->buildClaimsCollection()->toPlainArray()['sub']);
            } catch (JWTException $exception) {
               // 如果捕获到此异常，即代表 refresh 也过期了，用户无法刷新令牌，需要重新登录。
                throw new UnauthorizedHttpException('jwt-auth', $exception->getMessage());
            }
        }

        // 在响应头中返回新的 token
        return $this->setAuthenticationHeader($next($request), $token);
    }
}


```

> 当过期之后，会返回一个新的 token 到 headers 中，前端可以判断返回的 headers 中是否有 token，有的话存储，下次需要时候就提交新的 token。

### 设置 Axios 拦截器[#](#3692979764)

我选用的 HTTP 请求套件是 axios。为了达到无痛刷新 token 的效果，我们需要对 axios 定义一个拦截器，用以接收我们刷新的 Token，代码如下：

_app.js_

```
import Vue from 'vue'
import router from './router'
import store from './store'
import iView from 'iview'
import 'iview/dist/styles/iview.css'

Vue.use(iView)

new Vue({
    el: '#app',
    router,
    store,
    created() {
        // 自定义的 axios 响应拦截器
        this.$axios.interceptors.response.use((response) => {
            // 判断一下响应中是否有 token，如果有就直接使用此 token 替换掉本地的 token。你可以根据你的业务需求自己编写更新 token 的逻辑
            var token = response.headers.authorization
            if (token) {
                // 如果 header 中存在 token，那么触发 refreshToken 方法，替换本地的 token
                this.$store.dispatch('refreshToken', token)
            }
            return response
        }, (error) => {
            switch (error.response.status) {

                // 如果响应中的 http code 为 401，那么则此用户可能 token 失效了之类的，我会触发 logout 方法，清除本地的数据并将用户重定向至登录页面
                case 401:
                    return this.$store.dispatch('logout')
                    break
                // 如果响应中的 http code 为 400，那么就弹出一条错误提示给用户
                case 400:
                    return this.$Message.error(error.response.data.error)
                    break
            }
            return Promise.reject(error)
        })
    }
})


```

**Vuex** 内的代码如下：

_store/index.js_

```
import Vue from 'vue'
import Vuex from 'vuex'
import axios from 'axios'

Vue.use(Vuex)

export default new Vuex.Store({
    state: {
        name: null,
        avatar: null,
        mobile: null,
        token: null,
        remark: null,
        auth: false,
    },
    mutations: {
        // 用户登录成功，存储 token 并设置 header 头
        logined(state, token) {
            state.auth = true
            state.token = token
            localStorage.token = token
        },
        // 用户刷新 token 成功，使用新的 token 替换掉本地的token
        refreshToken(state, token) {
            state.token = token
            localStorage.token = token
            axios.defaults.headers.common['Authorization'] = state.token
        },
        // 登录成功后拉取用户的信息存储到本地
        profile(state, data) {
            state.name = data.name
            state.mobile = data.mobile
            state.avatar = data.avatar
            state.remark = data.remark
        },
        // 用户登出，清除本地数据
        logout(state){
            state.name = null
            state.mobile = null
            state.avatar = null
            state.remark = null
            state.auth = false
            state.token = null

            localStorage.removeItem('token')
        }
    },
    actions: {
         // 登录成功后保存用户信息
        logined({dispatch,commit}, token) {
            return new Promise(function (resolve, reject) {
                commit('logined', token)
                axios.defaults.headers.common['Authorization'] = token
                dispatch('profile').then(() => {
                    resolve()
                }).catch(() => {
                    reject()
                })
            })
        },
        // 登录成功后使用 token 拉取用户的信息
        profile({commit}) {
            return new Promise(function (resolve, reject) {
                axios.get('profile', {}).then(respond => {
                    if (respond.status == 200) {
                        commit('profile', respond.data)
                        resolve()
                    } else {
                        reject()
                    }
                })
            })
        },
        // 用户登出，清除本地数据并重定向至登录页面
        logout({commit}) {
            return new Promise(function (resolve, reject) {
                commit('logout')
                axios.post('auth/logout', {}).then(respond => {
                    Vue.$router.push({name:'login'})
                })
            })
        },
        // 将刷新的 token 保存至本地
        refreshToken({commit},token) {
            return new Promise(function (resolve, reject) {
                commit('refreshToken', token)
            })
        },
    }
})


```

### 更新异常处理的 Handler[#](#3566148436)

由于我们构建的是 api 服务，所以我们需要更新一下 app/Exceptions/Handler.php 中的 render

方法，自定义处理一些异常。

Handler.php

```
<?php

namespace App\Exceptions;

use Exception;
use Illuminate\Foundation\Exceptions\Handler as ExceptionHandler;
use Illuminate\Validation\ValidationException;
use Symfony\Component\HttpKernel\Exception\UnauthorizedHttpException;

class Handler extends ExceptionHandler
{
    ...

    /**
     * Render an exception into an HTTP response.
     *
     * @param  \Illuminate\Http\Request $request
     * @param  \Exception $exception
     * @return \Illuminate\Http\Response
     */
    public function render($request, Exception $exception)
    {
        // 参数验证错误的异常，我们需要返回 400 的 http code 和一句错误信息
        if ($exception instanceof ValidationException) {
            return response(['error' => array_first(array_collapse($exception->errors()))], 400);
        }
        // 用户认证的异常，我们需要返回 401 的 http code 和错误信息
        if ($exception instanceof UnauthorizedHttpException) {
            return response($exception->getMessage(), 401);
        }

        return parent::render($request, $exception);
    }
}


```

更新完此方法后，我们上面自定义的中间件里抛出的异常和我们下面参数验证错误抛出的异常都会被转为指定的格式抛出。

### 使用[#](#2759601843)

现在，我们可以在我们的 routes/api.php 路由文件中新增几条路由来测试一下了：

**api.php 新增路由 测试 token 刷新**

> 将 token 过期时间设置为 1 分钟，然后使用拿到的 token 去验证一下刷新 token 。

**路由文件**

```
.
.
.

//需要 token 验证的接口
    Route::group(['middleware'=>['jwt.auth']],function (){

        Route::any('jiekou','TestController@jiekou');

    });


    //刷新token的接口 前端每次请求都需要带上这个接口
    Route::middleware('refresh.token')->group(function (){

        Route::any('profile','UserController@profile');
    });



```

**在 app/kernel.php 文件下添加路由中间件：**

```
protected $routeMiddleware = [
        .
        .
        .
        'refresh.token'=> \App\Http\Middleware\RefreshToken::class,
    ];




```

> jwt.auth "Token not provided"，"Token has expired"

[![](https://img2020.cnblogs.com/blog/2137920/202105/2137920-20210511181831680-1341641919.png)](https://img2020.cnblogs.com/blog/2137920/202105/2137920-20210511181831680-1341641919.png)

token 的创建以及解析[#](#2014659917)
-----------------------------

**使用 token**

有两种使用方法：

*   加到 url 中：?token = 你的 token
*   加到 header 中，建议用这种，因为在 https 情况下更安全：Authorization:Bearer 你的 token

### **token 的创建**[#](#3095461400)

前面的 AuthController.php 中有两行展现了这一种 token 的创建方法，即用用户所给的账号和密码进行尝试，密码正确则用对应的 User 信息返回一个 token 。

但 token 的创建方法不止这一种，接下来介绍 token 的三种创建方法：

*   基于账密参数
*   基于 users 模型返回的实例
*   基于 users 模型中的用户主键 id

**a) 基于账密参数**

```
// 使用辅助函数
$credentials = request(['email', 'password']); 
$token = auth()->attempt($credentials)

// 使用 Facade
$credentials = $request->only('email', 'password');
$token = JWTAuth::attempt($credentials);


```

**b) 基于 users 模型返回的实例**

```
// 使用辅助函数
$user = User::first();
$token = auth()->login($user);

// 使用 Facade
$user = User::first();
$token = JWTAuth::fromUser($user);


```

**c) 基于 users 模型中的主键 id**

```
// 使用辅助函数
$token = auth()->tokenById(1);

// 使用 Facade
源码中没找到


```

### token 的解析[#](#693948508)

**a) 解析 token 到对象**

只有 Facade 需要这样。

```
// 把请求发送过来的直接解析到对象
JWTAuth::parseToken();


```

**b) 获取 token 中的 user 信息**

```
// 辅助函数
$user = auth()->user();

// Facade
$user = JWTAuth::parseToken()->authenticate();


```

**c) 获取 token**

如果 token 被设置则会返回，否则会尝试使用方法从请求中解析 token ，如果 token 未被设置或不能解析最终返回 false。

```
// 辅助函数
$token = auth()->getToken();

// Facade
$token = JWTAuth::parseToken()->getToken();


```

**d) 如果是前端**

直接 base64 解码 token 的前两段即可以知道所需的信息。因为载荷是用 Base64Url 编码，所以相当于明文，因此绝对不能放密码等敏感信息。

```
iss(Issuser)：代表这个JWT的签发主体；
sub(Subject)：代表这个JWT的主体，即它的所有人；
aud(Audience)：代表这个JWT的接收对象；
exp(Expiration time)：是一个时间戳，代表这个JWT的过期时间；
nbf(Not Before)：是一个时间戳，代表这个JWT生效的开始时间，意味着在这个时间之前验证JWT是会失败的；
iat(Issued at)：是一个时间戳，代表这个JWT的签发时间；
jti(JWT ID)：是JWT的唯一标识



```

**php base64_decode() 第二段会解析出过期时间戳。**

### token 的三个时间[#](#303176235)

一个 token 一般来说有三个时间属性，其配置都在 `config/jwt.php` 内。

**有效时间**

有效时间指的的是你获得 token 后，在多少时间内可以凭这个 token 去获取内容，逾时无效。

```
// 单位：分钟
'ttl' => env('JWT_TTL', 60)


```

**刷新时间**

刷新时间指的是在这个时间内可以凭旧 token 换取一个新 token。例如 token 有效时间为 60 分钟，刷新时间为 20160 分钟，在 60 分钟内可以通过这个 token 获取新 token，但是超过 60 分钟是不可以的，然后你可以一直循环获取，直到总时间超过 20160 分钟，不能再获取。

```
// 单位：分钟
'refresh_ttl' => env('JWT_REFRESH_TTL', 20160)


```

**宽限时间**

宽限时间是为了解决并发请求的问题，假如宽限时间为 0s ，那么在新旧 token 交接的时候，并发请求就会出错，所以需要设定一个宽限时间，在宽限时间内，旧 token 仍然能够正常使用。

```
// 宽限时间需要开启黑名单（默认是开启的），黑名单保证过期token不可再用，最好打开
'blacklist_enabled' => env('JWT_BLACKLIST_ENABLED', true)

// 设定宽限时间，单位：秒
'blacklist_grace_period' => env('JWT_BLACKLIST_GRACE_PERIOD', 60)


```

JWT 的 两个 Facade[#](#946502721)
------------------------------

### JWTAuth[#](#2751424612)

**JWTAuth::parseToken()->方法 () 一般都可以换成 auth()-> 方法()。**

#### token 生成

**attempt**

根据 user 账密新建一个 token。

```
$credentials = $request->only('email', 'password');
$token = JWTAuth::attempt($credentials)；


```

**fromUser or fromSubject**

根据 user 对象生成一个 token。后者是前者别名。

```
$user = User::find(1);
$token = JWTAuth::fromUser($user);


```

#### token 控制#

**refresh**

更新 token。

```
$newToken = JWTAuth::parseToken()->refresh();


```

**invalidate**

让一个 token 无效。

```
JWTAuth::parseToken()->invalidate();


```

**check**

检验 token 的有效性。

```
if(JWTAuth::parseToken()->check()) {
    dd("token是有效的");
}


```

#### token 解析

**authenticate or toUser or user**

这三个效果是一样的，toUser 是 authenticate 的别名，而 user 比前两者少一个 user id 的校验，但并没有什么影响。

```
$user = JWTAuth::parseToken()->toUser();


```

**parseToken**

从 request 中解析 token 到对象中，以便进行下一步操作。

```
JWTAuth::parseToken();


```

**getToken**

从 request 中获取 token。

```
$token = JWTAuth::getToken();  // 这个不用 parseToken ，因为方法内部会自动执行一次


```

### JWTGuard[#](#2522413751)

这个 Facade 主要进行载荷的管理，返回一个载荷对象，然后可以通过 JWTAuth 来对其生成一个 token。

```
// 载荷的高度自定义
$payload = JWTFactory::sub(123)->aud('foo')->foo(['bar' => 'baz'])->make();
$token = JWTAuth::encode($payload);


```

```
$customClaims = ['foo' => 'bar', 'baz' => 'bob'];
$payload = JWTFactory::make($customClaims);
$token = JWTAuth::encode($payload);


```

#### 其他一些用法

这里用 auth 的写法，**因为 Laravel 有多个 guard，默认 guard 也不是 api ，所以需要写成 auth('api') 否则，auth() 即可。**

设置载荷

```
$token = auth('api')->claims(['foo' => 'bar'])->attempt($credentials);


```

验证账密是否正确

```
$boolean = auth('api')->validate($credentials);


```

### jwt.php 配置项详解[#](#254432329)

```
<?php

return [

    /*
    |--------------------------------------------------------------------------
    | JWT Authentication Secret
    |--------------------------------------------------------------------------
    |
    | 用于加密生成 token 的 secret
    |
    */

    'secret' => env('JWT_SECRET'),

    /*
    |--------------------------------------------------------------------------
    | JWT Authentication Keys
    |--------------------------------------------------------------------------
    |
    | 如果你在 .env 文件中定义了 JWT_SECRET 的随机字符串
    | 那么 jwt 将会使用 对称算法 来生成 token
    | 如果你没有定有，那么jwt 将会使用如下配置的公钥和私钥来生成 token
    |
    */

    'keys' => [

        /*
        |--------------------------------------------------------------------------
        | Public Key
        |--------------------------------------------------------------------------
        |
        | 公钥
        |
        */

        'public' => env('JWT_PUBLIC_KEY'),

        /*
        |--------------------------------------------------------------------------
        | Private Key
        |--------------------------------------------------------------------------
        |
        | 私钥
        |
        */

        'private' => env('JWT_PRIVATE_KEY'),

        /*
        |--------------------------------------------------------------------------
        | Passphrase
        |--------------------------------------------------------------------------
        |
        | 私钥的密码。 如果没有设置，可以为 null。
        |
        */

        'passphrase' => env('JWT_PASSPHRASE'),

    ],

    /*
    |--------------------------------------------------------------------------
    | JWT time to live
    |--------------------------------------------------------------------------
    |
    | 指定 access_token 有效的时间长度（以分钟为单位），默认为1小时，您也可以将其设置为空，以产生永不过期的标记，
    |  注意：Notice: If you set this to null you should remove 'exp' element from 'required_claims' list.
    */

    'ttl' => env('JWT_TTL', 60),

    /*
    |--------------------------------------------------------------------------
    | Refresh time to live
    |--------------------------------------------------------------------------
    |
    | 指定 access_token 可刷新的时间长度（以分钟为单位）。默认的时间为 2 周。
    | 大概意思就是如果用户有一个 access_token，那么他可以带着他的 access_token 
    | 过来领取新的 access_token，直到 2 周的时间后，他便无法继续刷新了，需要重新登录。
    |
    */

    'refresh_ttl' => env('JWT_REFRESH_TTL', 20160),

    /*
    |--------------------------------------------------------------------------
    | JWT hashing algorithm
    |--------------------------------------------------------------------------
    |
    | 指定将用于对令牌进行签名的散列算法。
    |
    */

    'algo' => env('JWT_ALGO', 'HS256'),

    /*
    |--------------------------------------------------------------------------
    | Required Claims
    |--------------------------------------------------------------------------
    |
    | 指定必须存在于任何令牌中的声明。
    | 
    |
    */

    'required_claims' => [
        'iss',
        'iat',
        'exp',
        'nbf',
        'sub',
        'jti',
    ],

    /*
    |--------------------------------------------------------------------------
    | Persistent Claims
    |--------------------------------------------------------------------------
    |
    | 指定在刷新令牌时要保留的声明密钥。
    |
    */

    'persistent_claims' => [
        // 'foo',
        // 'bar',
    ],

    /*
    |--------------------------------------------------------------------------
    | Blacklist Enabled
    |--------------------------------------------------------------------------
    |
    | 为了使令牌无效，您必须启用黑名单。
    | 如果您不想或不需要此功能，请将其设置为 false。
    |
    */

    'blacklist_enabled' => env('JWT_BLACKLIST_ENABLED', true),

    /*
    | -------------------------------------------------------------------------
    | Blacklist Grace Period
    | -------------------------------------------------------------------------
    |
    | 当多个并发请求使用相同的JWT进行时，
    | 由于 access_token 的刷新 ，其中一些可能会失败
    | 以秒为单位设置请求时间以防止并发的请求失败。
    |
    */

    'blacklist_grace_period' => env('JWT_BLACKLIST_GRACE_PERIOD', 0),

    /*
    |--------------------------------------------------------------------------
    | Providers
    |--------------------------------------------------------------------------
    |
    | 指定整个包中使用的各种提供程序。
    |
    */

    'providers' => [

        /*
        |--------------------------------------------------------------------------
        | JWT Provider
        |--------------------------------------------------------------------------
        |
        | 指定用于创建和解码令牌的提供程序。
        |
        */

        'jwt' => Tymon\JWTAuth\Providers\JWT\Namshi::class,

        /*
        |--------------------------------------------------------------------------
        | Authentication Provider
        |--------------------------------------------------------------------------
        |
        | 指定用于对用户进行身份验证的提供程序。
        |
        */

        'auth' => Tymon\JWTAuth\Providers\Auth\Illuminate::class,

        /*
        |--------------------------------------------------------------------------
        | Storage Provider
        |--------------------------------------------------------------------------
        |
        | 指定用于在黑名单中存储标记的提供程序。
        |
        */

        'storage' => Tymon\JWTAuth\Providers\Storage\Illuminate::class,

    ],

];


```

跨域[#](#2745848410)
------------------

> 详细配置过程，请查看官方文档。

*   安装 [barryvdh/laravel-cors](https://github.com/fruitcake/laravel-cors)
*   配置 cors

**在配置文件 cors.php 中，设置 exposedHeaders，不然无法跨域获取到 header 中的 Authorization 值来刷新前端令牌。**

```
'exposedHeaders' => ['Authorization'],


```

刷新时间[#](#2097180541)
--------------------

```
刷新时间指的是在这个时间内可以凭旧 token 换取一个新 token。例如 token 有效时间为 60 分钟，刷新时间为 20160 分钟。代表你的 token 在 60 分钟内可以正常使用，超过 60 分钟则过期无法使用。但在 20160 分钟的期限内你可以在任意时刻凭旧 token 换取新 token。
刷新token后旧的token不能用了。

其实刷新 Token 就是将旧 Token 放入黑名单，然后生成一个新的 Token，
tymon/jwt-auth/src/Manager.php
https://learnku.com/articles/12679/jwt-auth-blacklist-function
同一时间只允许登录唯一一台设备。例如设备 A 中用户如果已经登录，那么使用设备 B 登录同一账户，设备 A 就无法继续使用了。
我们可以给用户表新增一个字段，或者单独使用一张表，总之是需要先将用户的 Token 存下来，那么下次用户再次登录时，调用如下代码将旧 Token 加入黑名单：

\JWTAuth::setToken($oldToken)->invalidate();
然后将新的 Token 保存下来，最后将 Token 返回。
这里会有一个问题，如果这个用户长时间没登录过，Token 已无效。再用这个 oldToken 去加入黑名单会报错。

可以这样写
/* @var \Tymon\JWTAuth\JWTGuard $apiGuard */
$apiGuard = auth('api');
$apiGuard->setToken($oldToken);

 // 检查旧 Token 是否有效
 if ($apiGuard->check()) {
    // 加入黑名单
    $apiGuard->invalidate();
 }

根据一个 token 串获取用户是否已认证，可以使用：
$ok = JWTAuth::setToken('your_token_string')->check(); // true or false


```