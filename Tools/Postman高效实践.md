>  原文地址 [blog.csdn.net](https://blog.csdn.net/weixin_43793874/article/details/97495674)

### 文章目录

*   *   [一、概述](#_1)
    *   [二、请求](#_20)
    *   *   [1. 请求](#1__24)
        *   *   [1.1 API](#11_API_26)
            *   [1.2 请求体](#12__49)
            *   [1.3 Cookies](#13_Cookies_69)
            *   [1.4 接口权限](#14__73)
        *   [2. 响应](#2__88)
        *   *   [2.1 API](#21_API_90)
            *   [2.2 响应体](#22__104)
            *   [2.3 保存响应结果](#23__116)
        *   [3. 抓包](#3__122)
    *   [三、集合](#_131)
    *   *   [1. 创建集合](#1__140)
        *   [2. 管理集合](#2__152)
        *   *   [2.1 集合详情](#21__154)
            *   [2.2 集合菜单](#22__169)
            *   [2.3 版本管理](#23__185)
            *   *   [2.3.1 Fork 集合](#231_Fork_189)
                *   [2.3.2 拉取修改](#232__194)
                *   [2.3.3 合并修改](#233__198)
                *   [2.3.4 解决冲突](#234__208)
        *   [3. 导入集合](#3__214)
        *   *   [3.1 导出数据](#31__218)
            *   [3.2 导入数据](#32__228)
    *   [四、脚本](#_240)
    *   *   [1. 前置脚本](#1__253)
        *   [2. 测试脚本](#2__267)
        *   [3. 测试示例](#3__323)
        *   [4. 脚本 API](#4_API_514)
        *   *   [4.1 require](#41_require_518)
            *   [4.2 pm](#42_pm_566)
            *   [4.3 测试脚本](#43__657)
            *   [4.4 响应断言](#44__715)
    *   [五、变量](#_747)
    *   *   [1. 变量](#1__762)
        *   *   [1.1 变量作用域](#11__764)
            *   [1.2 在请求中使用变量](#12__776)
            *   [1.3 在脚本中使用变量](#13__780)
            *   [1.4 定义集合变量](#14__788)
            *   [1.5 导入数据文件的变量](#15__792)
            *   [1.6 内置变量](#16__796)
        *   [2. 环境和全局变量](#2__806)
        *   *   [2.1 创建环境和全局变量](#21__810)
            *   [2.2 选择环境](#22__816)
            *   [2.3 查看环境和全局变量](#23__820)
        *   [3. 作用域和优先级](#3__824)
    *   [六、运行](#_832)
    *   *   [1. 运行集合](#1__836)
        *   [2. 导入数据](#2__842)
        *   [3. 迭代运行](#3__856)
        *   [4. 工作流](#4__860)
        *   [5. 命令行](#5__864)
    *   [七、文档](#_896)
    *   [八、Mock](#Mock_900)
    *   [九、工作空间](#_921)
    *   [十、实践](#_925)

一、概述
----

Postman 是一款优秀的 HTTP 接口测试软件，它最初是谷歌浏览器的插件，后来独立成 PC 软件，支持多平台（macOS / Windows / Linux）安装。测试对于开发人员开发高质量的应用不可或缺，对于后端开发，除了必要的单元测试之外，也应对 HTTP 接口层进行相应的测试，对于前端开发，除了对接口进行检验外，也可以 Mock 接口服务，达到与后端并行开发的目的。

**准备**：学习前需掌握基本的 HTTP 协议和请求响应模型

**设置**：在导航工具栏右侧打开设置面板  
![](https://img-blog.csdnimg.cn/2019072711081691.png)

*   通用
    *   [x] Trim keys and values in request body：请求体中的键和值去掉首尾空格
    *   [x] Send no-cache header：设置不缓存和请求头，保存每个响应都是最新的
*   主题：配置显示主题
*   快捷键：查看快捷键
*   数据：导入 / 导出数据
*   插件：下载 Newman 插件
*   证书：基于域名添加和查看客户端证书
*   代理：配置代理
*   更新：配置自动更新

二、请求
----

Postman 对接口测试就是创建一个 HTTP 接口的请求（Request），然后发送请求，检验响应结果。在 Postman 中，每个请求（或请求集合）都自己的名称和描述。

### 1. 请求

![](https://img-blog.csdnimg.cn/20190727110913595.png)

#### 1.1 API

我们可以把 API 接口签名定义为：`Method + URL`，不管是普通接口还是 RESTful 风格设计的接口，两个不同的 API 不可能存在相同的请求方法和请求 URL。

**URL**：在接口请求窗口直接输入 URL，可以在 **Params** 标签页可以添加 URL 请求参数，如果是 RESTful 风格接口，可以使用`:`添加 **Path Variable**：

```
https://api.library.com/:entity/

```

对于 URL 需要注意：

*   如果没有输入协议，Postman 会自动添加`http://`
*   Postman 会自动对 URL 或 URL 请求参数进行编码，可以选中文本右击`EncodeURIComponent`来手动进行编码

**Headers**：在 **Headers** 标签页中就可以管理请求头

> 点击`Presets`来管理常用的请求头，可以对请求头进行预设置，比如多个接口使用相同的请求头。

**Method**：在 URL 输入栏左侧下拉框设置请求方式。

> 可以在侧边栏`History`中查看请求历史记录

#### 1.2 请求体

Postman 提供几乎所有的请求体，在 **Body** 标签页中配置接口请求的请求体，在你选择不同的请求体格式时，Postman 会自动添加对应的`Content-Type`请求头，Binary 形式的请求体不会自动添加。

**None**：（默认）没有请求体

**Form-data**：配置`multipart/form-data`格式的请求体，支持文件上传

**Urlencoded**：配置与 URL 请求参数相同的编码格式，不支持文件上传

**Raw**：原生请求体，Postman 不会对请求体做任何修改，除了替换变量之外

**Binary**：配置无法输入的内容，比如：文本、图像、音频、视频文件

> 使用 **Raw** 格式请求体可以选择不同内容格式来高亮显示请求体内容，比如 XML、JSON 等，还可以点击 **Beautify**（`Ctrl+B`）来格式化内容。
>
> URL 请求参数格式：`a=b&b=c&b=d`，GET 请求是将参数使用`?`号连接放置在 URL 后面，POST 请求的 Urlencoded 方式则是将参数放置在请求体里。
>
> 在输入参数的时候可以直接将 `JSON` 复制粘贴到参数表格中，可以快速输入参数。

#### 1.3 Cookies

在请求标签栏右侧单击 **Cookies** 可以管理请求的 Cookie。  
![](https://img-blog.csdnimg.cn/20190727110950841.png)

#### 1.4 接口权限

**Authorization** 标签页用来配置接口权限请求头，支持多种权限：

*   Inherit auth from parent
*   No Auth
*   Bearer Token
*   Basic auth
*   Digest Auth
*   OAuth 1.0
*   OAuth 2.0
*   Hawk Authentication
*   AWS Signature
*   NTLM Authentication [Beta]

### 2. 响应

![](https://img-blog.csdnimg.cn/20190727111041775.png)

#### 2.1 API

响应是请求接口后的返回，Postman 支持查看多种类型响应，JSON、XML、HTML 等，支持格式化和预览功能。

**Headers**：在响应的 **Headers** 标签页中可以查看响应的请求头

**响应时间**：在响应的标签页右侧可以查看接口的响应时间

**响应大小**：在响应的标签页右侧可以查看接口的响应内存大小

**Cookies**：在响应的 **Cookies** 标签页查看接口响应的 Cookie

**Tests**：在响应的 **Test Results** 标签页查看接口测试的结果

#### 2.2 响应体

在 **Body** 标签页中查看响应的响应体，Postman 也支持种查看格式。

**Pretty**：对响应体内容格式化显示，比如 JSON、XML 等

**Raw**：原生显示，Postman 对响应体不会做任何修改

**Preview**：预览显示，Postman 会对 HTML 响应进行渲染

> 在 **Pretty** 显示模式中，如果想要 Postman 自动格式化显示 JSON、XML 等响应体，确保接口响应返回相应的`Content-Type`，如果没有，你需要手动强制转换，也可以通过设置 Settings | General | REQUEST | Language detection 来设置默认返回类型。

#### 2.3 保存响应结果

![](https://img-blog.csdnimg.cn/201907271111381.png)  
点击标签页右侧的 **Save** 按钮可以保存请求该次的响应结果，保存后可以通过点击窗口右上角的 **Examples** 来查看该接口保存的响应结果。

> 接口保存的响应结果可以用作 Mock 时接口的返回结果。

### 3. 抓包

Postman 支持代理服务器功能，手机等客户端可以通过连接 Postman 代理服务器实现抓包功能。  
![](https://img-blog.csdnimg.cn/20190727111207502.png)  
点击导航栏右侧工具栏中的雷达按钮，通过配置端口和过滤规则，以及 Target，一般设置为 History，这样就可以在 History 请求历史记录里面查看拦截客户端的请求，这样简单的配置后，当前 PC 就可以当作代理服务器了，然后手机网络配置代理为当前 PC 的 IP 地址和前面配置的端口，就可以实现抓包。  
![](https://img-blog.csdnimg.cn/20190727111235830.png)

> Postman 可以通过当前请求来生成各种语言的代码片段，点击请求的标签页右侧的 **Code** 来创建，通过选择左上角的下拉列表来选择不同的语言或框架。

三、集合
----

![](https://img-blog.csdnimg.cn/2019072711130841.png)  
Postman 的集合可以对一组请求分类，也可以为一类请求创建文件夹。为什么要创建集合？

*   **组织**：你可以按类保存你的请求，而不需要每次通过历史记录去搜索自己的请求
*   **文档**：你可以为请求、文件夹、集合添加名称和描述，在集合中可以浏览生成的请求文档
*   **测试**：你可以针对某类请求添加测试脚本，或者整合测试用例
*   **工作流**：你可以使用脚本在 API 请求之间传递数据，构建和 API 实际操作相符的工作流

### 1. 创建集合

![](https://img-blog.csdnimg.cn/20190727111341691.png)  
单击侧边栏的 **+ New Collection** 或工具栏第一个 **+ New** 按钮可以创建一个集合，创建集合的时候可以针对集合添加下面内容：

*   输入集合的名称和描述
*   配置集合的授权类型
*   配置集合的前置脚本
*   配置集合的测试脚本
*   配置集合的变量

> 集合的授权、脚本和变量对该集合下的所有请求有效。针对集合的配置，可以抽象该集合下请求的公共配置，从而减少配置量，至于脚本的执行后面会说到。

### 2. 管理集合

![](https://img-blog.csdnimg.cn/201907271114123.png)

#### 2.1 集合详情

单击集合右侧的 ▸图标打开集合的详情栏，集合名称下面存在以后按钮菜单和标签页，按钮菜单如下：

*   **Share**：分享集合
*   **Run**：运行集合，批量运行请求，后面介绍
*   **View in web**：在 Web 页面中查看

标签页如下：

*   **Documentation**：接口文档
*   **Monitors**：监控
*   **Mocks**：Mock 服务
*   **Changelog**：修改日志

#### 2.2 集合菜单

单击集合右侧的菜单 **…** 打开集合的菜单项，除去与详情页相同的功能菜单外如下：

*   **Rename**：重命名
*   **Edit**：编辑
*   **Create a fork**：创建一个 Fork 仓库
*   **Merge changes**：将当前 Fork 仓库的修改合并至主仓库
*   **Add Reauest**：添加一个请求
*   **Add Folder**：添加一个文件夹
*   **Duplicate**：复制当前集合
*   **Export**：导出当前集合
*   **Remove form workspace**：从工作空间删除

> Postman 中对请求、文件夹和集合添加描述时，可以使用 Markdown 语言来编写，支持插入图片等内容，在生成接口文档的时候就可以很好得展示描述内容。

#### 2.3 版本管理

版本管理和 Git 原理一样，在多人协作的时候，可以从主集合 Fork 一个一样的集合出来，各自维护，然后可以将各自的修改合并到主集合上，也可以从主集合上摘取最新的集合版本。你可以从个人的工作空间或团队的工作空间中 Fork 一个集合，前提是工作空间属于你或者你是团队工作空间的一个成员。

##### 2.3.1 Fork 集合

点击集合的菜单项选择 **Create a fork** 来创建一个 Fork 集合。  
![](https://img-blog.csdnimg.cn/20190727111437491.gif)

> Fork 集合的时候不会 Fork 集合的 Mock 数据和监控数据，如果你要使用需要重新创建。

##### 2.3.2 拉取修改

在 Fork 集合后，如果主集合被其他人更新了，此时 Fork 集合和主集合就不会一样，在这种情况下，可以点击 Fork 集合的菜单选择 **Merge changes** 来拉取最新的修改。

##### 2.3.3 合并修改

在 Fork 集合后，如果你对 Fork 集合做了一个修改，想要将修改合并到主集合上面去，可以点击 Fork 集合的菜单选择 **Merge changes** 来操作。

在合并修改的时候有下面三个选择：

*   **Merge changes:** 将 Fork 集合的修改合并到主集合
*   **Merge changes and update source:** 将 Fork 集合的修改合并到主集合并更新 Fork 集合
*   **Merge changes and delete source:** 将 Fork 集合的修改合并到主集合并删除 Fork 集合

##### 2.3.4 解决冲突

当两个或以上用户编辑了同一个请求、文件夹或集合时，在合并集合的时候会产生冲突，在这种情况下，我们在合并集合的时候需要先解决冲突在进行合并操作。  
![](https://img-blog.csdnimg.cn/20190727111511103.png)

> 如果你对 Git 有所了解，对版本管理这一节掌握没有太大问题。

### 3. 导入集合

Postman 可以导入和导出集合、环境、全局变量和请求头预配置，也可以通过文件和 URL 形式资源导入其他格式的 API 数据，比如 Swagger、WADL 等等。

#### 3.1 导出数据

Postman 可以以 JSON 文件形式导出所有数据，包括各种元数据，你可以重新导入 JSON 文件来创建集合，或者使用 Newman 在命令行界面运行该 JSON 文件。

**导出集合**：点击集合菜单的 **Export** 来导出集合数据

**导出环境**：打开 **Manage Environment** 界面来配置和导出环境数据  
![](https://img-blog.csdnimg.cn/20190727111537302.png)  
**导出数据**：打开**设置**菜单，点击 Data | Export data | Download 来导出全部数据，包括所有集合、环境、全局变量和请求头预配置等数据。

#### 3.2 导入数据

Postman 可以通过文件或 URL 资源来导入 API 数据，导入的数据格式可以是多种多样的，比如：

*   Postman：通过 Postman 设置导出的或集合菜单导出的数据
*   cURL：有效的 cURL（只能是 HTTP 协议）命令
*   RAML：将 RAML 定义的 API 数据导入成集合
*   Swagger：将 Swagger 生成的 API 数据导入成集合
*   WADL：导入 WADL 规范的 API 数据

**导入步骤：**  
![](https://img-blog.csdnimg.cn/20190727111606834.png)

四、脚本
----

Postman 基于 Node.js 开发了一个强大的运行脚本平台，可以在请求和集合运行前后添加更多的动态操作，你可以使用它来编写测试用例、构建请求，你可以在下面两个事件执行的时候添加 JavaScript 代码：

*   在请求发送到服务器之前，可以在 **Pre-request Script** 标签页添加前置脚本
*   在响应到达之后，可以在 **Tests** 标签页上添加测试脚本  
    ![](https://img-blog.csdnimg.cn/20190727111652840.png)  
    你不仅可以在每个请求中添加脚本，还可以在文件夹、集合中添加脚本，脚本的执行顺序如下：  
    ![](https://img-blog.csdnimg.cn/20190727111731723.png)  
    在脚本中使用 `console.log('...')` 等打印日志的代码时，会在 Postman 的控制台输出，可以点击左下角的控制台（`Ctrl+Alt+C`）图标打开：  
    ![](https://img-blog.csdnimg.cn/20190727111751420.png)

> Postman 的脚本执行原理是 Postman 基于 Node.js 开发，并自带一个沙箱环境来运行 JavaScript 脚本

### 1. 前置脚本

前置脚本在发送请求之前执行，可以用来定义参数构建请求，比如在发送的请求中添加一个时间请求头：

* 在请求的 **Pre-request Script** 标签中编写下面代码：

  ```
  pm.globals.set("timestampHeader", new Date());
  
  ```

* 在请求头 **Headers** 标签添加 timestamp 请求头，并使用`{{timestampHeader}}`来使用变量  
  ![](https://img-blog.csdnimg.cn/20190727111822122.png)

> 不管是全局变量还是环境变量，还是集合的变量，都可以在 URL、表单、请求体等输入框通过 `{{var}}` 来使用变量。

### 2. 测试脚本

使用 JavaScript 语言来为每个请求编写测试。  
![](https://img-blog.csdnimg.cn/20190727111856596.png)  
在 Postman 发送请求之后，可以访问响应对象 `pm.response`，例如：

```
// pm.response.to.have
pm.test("response is ok", function () {
    pm.response.to.have.status(200);
});

// pm.expect()
pm.test("environment to be production", function () { 
    pm.expect(pm.environment.get("env")).to.equal("production"); 
});

// response 断言
pm.test("response should be okay to process", function () { 
    pm.response.to.not.be.error; 
    pm.response.to.have.jsonBody(""); 
    pm.response.to.not.have.jsonBody("error"); 
});

// pm.response.to.be*
pm.test("response must be valid and have a body", function () {
     pm.response.to.be.ok;
     pm.response.to.be.withBody;
     pm.response.to.be.json;
});

```

在编写脚本的时候虽然你只需要记住很少的一些代码，Postman 在编辑栏也提供了常用的代码片段，来帮助你更快的编写脚本。  
![](https://img-blog.csdnimg.cn/20190727111926574.png)  
在响应的 **Tests** 标签栏可以看到所有测试的结果。  
![](https://img-blog.csdnimg.cn/2019072711195450.png)  
在编辑集合或者文件夹的时候可以添加脚本。当你运行一个集合或文件夹时，里面包含多个请求，可以使用脚本来控制请求的工作流：

**设置当前请求执行完的下一个请求**

```
postman.setNextRequest("request_name");

```

**设置当前请求执行完后停止请求**

```
postman.setNextRequest(null);

```

使用 `postman.setNextRequest()` 注意的点：

1.  指定后续请求的名称或 ID，然后使用 **Runner** 来执行
2.  它可以在前置脚本和测试脚本中使用，如果指定了多个，只有最后一个有效
3.  如果没有`postman.setNextRequest()`，**Runner** 将按顺序执行后续请求

### 3. 测试示例

下面是常用的测试脚本：

**设置环境变量**

```
pm.environment.set("variable_key", "variable_value");

```

**设置嵌套对象环境变量**

```
var array = [1, 2, 3, 4];
pm.environment.set("array", JSON.stringify(array, null, 2));

var obj = { a: [1, 2, 3, 4], b: { c: 'val' } };
pm.environment.set("obj", JSON.stringify(obj));

```

**获取环境变量**

```
pm.environment.get("variable_key");

```

**获取嵌套对象环境变量**

```
var array = JSON.parse(pm.environment.get("array"));
var obj = JSON.parse(pm.environment.get("obj"));

```

**清除环境变量**

```
pm.environment.unset("variable_key");

```

**设置全局变量**

```
pm.globals.set("variable_key", "variable_value");

```

**获取全局变量**

```
pm.globals.get("variable_key");

```

**清除全局变量**

```
pm.globals.unset("variable_key");

```

**获取变量**：从环境变量和全局变量中搜索

```
pm.variables.get("variable_key");

```

**检验响应体中是否包含字符串**

```
pm.test("Body matches string", function () {
    pm.expect(pm.response.text()).to.include("string_you_want_to_search");
});

```

**检验响应体中是否等于字符串**

```
pm.test("Body is correct", function () {
    pm.response.to.have.body("response_body_string");
});

```

**检验响应的 JSON 的值**

```
pm.test("Your test name", function () {    var jsonData = pm.response.json();    pm.expect(jsonData.value).to.eql(100);});
```

**检验是否存在 Content-Type**

```
pm.test("Content-Type is present", function () {    pm.response.to.have.header("Content-Type");});
```

**检验响应时间是否小于 200 毫秒**

```
pm.test("Response time is less than 200ms", function () {    pm.expect(pm.response.responseTime).to.be.below(200);});
```

**检验状态码是否为 200**

```
pm.test("Status code is 200", function () {    pm.response.to.have.status(200);});
```

**检验状态码名称包含字符串**

```
pm.test("Status code name has string", function () {    pm.response.to.have.status("Created");});
```

**检验状态码是否在某个范围内**

```
pm.test("Successful POST request", function () {    pm.expect(pm.response.code).to.be.oneOf([201,202]);});
```

**使用 TinyValidator 校验 JSON 数据**

```
var schema = { "items": { "type": "boolean" }};var data1 = [true, false];var data2 = [true, 123];pm.test('Schema is valid', function() {  pm.expect(tv4.validate(data1, schema)).to.be.true;  pm.expect(tv4.validate(data2, schema)).to.be.true;});
```

**JSON 校验**

```
var Ajv = require('ajv'),    ajv = new Ajv({logger: console}),    schema = {        "properties": {            "alpha": {                "type": "boolean"            }        }    };pm.test('Schema is valid', function() {    pm.expect(ajv.validate(schema, {alpha: true})).to.be.true;    pm.expect(ajv.validate(schema, {alpha: 123})).to.be.false;});
```

**base64 解码**

```
var intermediate,	base64Content,	rawContent = base64Content.slice('data:application/octet-stream;base64,'.length);intermediate = CryptoJS.enc.Base64.parse(base64content);pm.test('Contents are valid', function() {  pm.expect(CryptoJS.enc.Utf8.stringify(intermediate)).to.be.true;});
```

**发送异步请求**：该函数可以用于前置脚本和测试脚本中

```
pm.sendRequest("https://postman-echo.com/get", function (err, response) {    console.log(response.json());});
```

**XML 转换成 JSON 对象**

```
var jsonObject = xml2Json(responseBody);
```

### 4. 脚本 API

Postman 脚本提供了简单实用的 API 用于测试和工作流。

#### 4.1 require

**require(moduleName:String):function → ***

`require`函数可以导入 Postman 沙箱内置的模块库，包含下列内置库：

*   [ajv](https://www.npmjs.com/package/ajv) → v6.6.2
*   [atob](https://www.npmjs.com/package/atob) → v2.1.2
*   [btoa](https://www.npmjs.com/package/btoa) → v1.2.1
*   [chai](http://chaijs.com/) → v4.2.0
*   [cheerio](https://cheerio.js.org/) → v0.22.0
*   [crypto-js](https://www.npmjs.com/package/crypto-js) → v3.1.9-1
*   [csv-parse/lib/sync](http://csv.adaltas.com/parse) → v1.2.4
*   [lodash](https://lodash.com/) → v4.17.11 (when used with require, the inbuilt `_` object is for v3.10.1)
*   [moment](http://momentjs.com/docs/) → v2.22.2 (sans locales)
*   [postman-collection](http://www.postmanlabs.com/postman-collection/) → v3.4.0
*   [tv4](https://github.com/geraintluff/tv4) → v1.3.0
*   [uuid](https://www.npmjs.com/package/uuid) → (the module loaded is a shim for original module)
*   [xml2js](https://www.npmjs.com/package/xml2js) → v0.4.19

一些 NodeJS 的模块也可以使用:

*   [path](https://nodejs.org/docs/latest-v11.x/api/path.html)
*   [assert](https://nodejs.org/docs/latest-v11.x/api/assert.html)
*   [buffer](https://nodejs.org/docs/latest-v11.x/api/buffer.html)
*   [util](https://nodejs.org/docs/latest-v11.x/api/util.html)
*   [url](https://nodejs.org/docs/latest-v11.x/api/url.html)
*   [punycode](https://nodejs.org/docs/latest-v11.x/api/punycode.html)
*   [querystring](https://nodejs.org/docs/latest-v11.x/api/querystring.html)
*   [string_decoder](https://nodejs.org/docs/latest-v11.x/api/string_decoder.html)
*   [stream](https://nodejs.org/docs/latest-v11.x/api/stream.html)
*   [timers](https://nodejs.org/docs/latest-v11.x/api/timers.html)
*   [events](https://nodejs.org/docs/latest-v11.x/api/events.html)

使用函数导入模块赋值给变量，例如：

```
var atob = require('atob'),    _ = require('lodash'),    arrayOfStrings,    base64Strings;arrayOfStrings = ['string1', 'string2'];  base64Strings = _.map(arrayOfStrings, atob);console.log(base64Strings);
```

#### 4.2 pm

**pm:Object**

`pm`对象封装了有关脚本执行的所有信息，可以用来获取请求、响应信息，也可以获取和设置环境环境变量和全局变量。

**[pm.info](http://pm.info):Object**

`pm.info`对象包含了脚本执行时的信息，比如请求名称，请求 ID 和迭代次数都保存在这对象中。

*   `pm.info.eventName:String`：执行脚本名称，前置脚本：prerequest，测试脚本：test
*   `pm.info.iteration:Number`：**Runner** 执行时的当前迭代计数
*   `pm.info.iterationCount:Number`：迭代执行时的迭代总数
*   `pm.info.requestName:String`
*   `pm.info.requestId:String`

**pm.sendRequest:Function**

`pm.sendRequest`函数可以用来发送 HTTP/HTTPS 的异步请求，异步请求，只是在后台处理一些繁重的复杂的逻辑，或是发送多个请求，而不要等待阻塞下一个请求，你可以使用回调函数来处理返回结果。该函数可以用于前置脚本和测试脚本中。

```
// 普通URLpm.sendRequest('https://postman-echo.com/get', function (err, res) {    if (err) {        console.log(err);    } else {        pm.environment.set("variable_key", "new_value");    }});// 构建SDK请求const echoPostRequest = {  url: 'https://postman-echo.com/post',  method: 'POST',  header: 'headername1:value1',  body: {    mode: 'raw',    raw: JSON.stringify({ key: 'this is json' })  }};pm.sendRequest(echoPostRequest, function (err, res) {  console.log(err ? err : res.json());});// 包含测试pm.sendRequest('https://postman-echo.com/get', function (err, res) {  if (err) { console.log(err); }  pm.test('response should be okay to process', function () {    pm.expect(err).to.equal(null);    pm.expect(res).to.have.property('code', 200);    pm.expect(res).to.have.property('status', 'OK');  });});
```

参考：

*   [Request JSON](http://www.postmanlabs.com/postman-collection/Request.html#~definition)
*   [Response Structure](http://www.postmanlabs.com/postman-collection/Response.html)

**pm.globals:VariableScope**

*   `pm.globals.has(variableName:String):function → Boolean`
*   `pm.globals.get(variableName:String):function → *`
*   `pm.globals.set(variableName:String, variableValue:String):function`
*   `pm.globals.unset(variableName:String):function`
*   `pm.globals.clear():function`
*   `pm.globals.toObject():function → Object`

**pm.environment:VariableScope**

*   `pm.environment.has(variableName:String):function → Boolean`
*   `pm.environment.get(variableName:String):function → *`
*   `pm.environment.set(variableName:String, variableValue:String):function`
*   `pm.environment.unset(variableName:String):function`
*   `pm.environment.clear():function`
*   `pm.environment.toObject():function → Object`

**pm.variables:VariableScope**

在 Postman 中，所有的变量定义都是有优先级的，在当前迭代请求中定义的变量优先于在环境中定义的变量，而环境变量优先于全局变量。优先级：`Iteration Data` > `Environment` >`Global`，在环境中或全局定义的变量都可以通过下面函数获取到。

*   `pm.variables.get(variableName:String):function → *`

**pm.request:Request**

`pm.request`对象包含请求信息，例如：

*   `pm.request.url:`[`Url`](http://www.postmanlabs.com/postman-collection/Url.html)
*   `pm.request.headers:`[`HeaderList`](http://www.postmanlabs.com/postman-collection/HeaderList.html)

#### 4.3 测试脚本

**pm.response:Response**

`pm.response`对象包含响应信息，例如：

*   `pm.response.code:Number`
*   `pm.response.reason():Function → String`
*   `pm.response.headers:`[`HeaderList`](http://www.postmanlabs.com/postman-collection/HeaderList.html)
*   `pm.response.responseTime:Number`
*   `pm.response.text():Function → String`
*   `pm.response.json():Function → Object`

**pm.iterationData:VariableScope**

`pm.iterationData`对象在使用 **Runner** 运行集合或文件夹时通过数据文件导入的数据。

*   `pm.iterationData.get(variableName:String):function → *`
*   `pm.iterationData.toObject():function → Object`

**pm.cookies:CookieList**

`cookies`对象可以获取到 Cookies 信息，例如：

* `pm.cookies.has(cookieName:String):Function → Boolean`

  检查在当前的请求域名中是否存在特定的 Cookie

* `pm.cookies.get(cookieName:String):Function → String`

  获取特定 Cookie 的值

* `pm.cookies.toObject:Function → Object`

  以对象形式获取 Cookies 的值

**pm.test(testName:String, specFunction:Function):Function**

你可以使用该函数来编写测试点，来检验响应是否符合预期：

```
pm.test("response should be okay to process", function () {
    pm.response.to.not.be.error;
    pm.response.to.have.jsonBody('');
    pm.response.to.not.have.jsonBody('error');
});

```

**pm.expect(assertion:*):Function → Assertion**

`pm.expect`是一个通用断言函数，使用 [ChaiJS expect BDD library](http://chaijs.com/api/bdd/) 标准，这个库可以让测试代码更具可读性。

```
pm.test('environment to be production', function () {
    pm.expect(pm.environment.get('env')).to.equal('production');
});

```

#### 4.4 响应断言

*   `pm.response.to.have.status(code:Number)`
*   `pm.response.to.have.status(reason:String)`
*   `pm.response.to.have.header(key:String)`
*   `pm.response.to.have.header(key:String, optionalValue:String)`
*   `pm.response.to.have.body()`
*   `pm.response.to.have.body(optionalValue:String)`
*   `pm.response.to.have.body(optionalValue:RegExp)`
*   `pm.response.to.have.jsonBody()`
*   `pm.response.to.have.jsonBody(optionalExpectEqual:Object)`
*   `pm.response.to.have.jsonBody(optionalExpectPath:String)`
*   `pm.response.to.have.jsonBody(optionalExpectPath:String, optionalValue:*)`
*   `pm.response.to.have.jsonSchema(schema:Object)`
*   `pm.response.to.have.jsonSchema(schema:Object, ajvOptions:Object)`

**[pm.response.to.be](http://pm.response.to.be).***

*   `pm.response.to.be.info`：检验 1XX 状态码
*   `pm.response.to.be.success`：检验 2XX 状态码
*   `pm.response.to.be.redirection`：检验 3XX 状态码
*   `pm.response.to.be.clientError`：检验 4XX 状态码
*   `pm.response.to.be.serverError`：检验 5XX 状态码
*   `pm.response.to.be.error`：检验 4XX 状态码或 5XX 状态码
*   `pm.response.to.be.ok`：检验状态码是否为 200
*   `pm.response.to.be.accepted`：检验状态码是否为 202
*   `pm.response.to.be.badRequest`：检验状态码是否为 400
*   `pm.response.to.be.unauthorized`：检验状态码是否为 401
*   `pm.response.to.be.forbidden`：检验状态码是否为 403
*   `pm.response.to.be.notFound`：检验状态码是否为 404
*   `pm.response.to.be.rateLimited`：检验状态码是否为 429

五、变量
----

Postman 支持设置各种不同作用域的变量，可以在脚本中引用这些变量，也可以在请求的 URL、参数、请求体等地方使用`{{var_name}}`来调用，Postman 可以在集合中设置变量，可以创建环境抽象来设置变量，也可以设置作用域最大的全局变量。简而言之，使用变量可以：

*   管理一些特殊的值，方便修改
*   根据不同的环境对同一变量设置不同的值
*   解析响应中的值，以及更灵活地构建一个请求

> 所有变量的值都只能是字符串类型，如果有复杂的对象类型，可以使用 JSON 字符串来设置。例如：
>
> ```
> var str = JSON.stringify(object);var obj = JSON.parse(str);
> ```

### 1. 变量

#### 1.1 变量作用域

Postman 中有 5 种作用域：

*   Global：全局变量
*   Collection：集合变量
*   Environment：环境变量
*   Data：导入数据的变量
*   Local/Tempoary：本地变量 / 临时变量

不同作用域有着不同的优先级，作用域的大小如下图所示，如果在两个作用域中有同名变量，取优先级大的作用域中的值，作用域越大，优先级越低。  
![](https://img-blog.csdnimg.cn/20190727112034985.png)

#### 1.2 在请求中使用变量

使用`{{variableName}}`在构建请求时的任务地方插入变量的值，比如接口的域名、参数、请求头等地方。例如：  
![](https://img-blog.csdnimg.cn/20190727112103961.png)

#### 1.3 在脚本中使用变量

在脚本中通过代码获取在各个作用域中设置的变量：

*   使用`pm.environment.get()`和`pm.environment.set()`来获取和设置环境变量
*   使用`pm.globals.get()`和`pm.globals.set()`来获取和设置全局变量
*   使用`pm.variables.get()`可以从任何作用域中搜索值，使用`pm.variables.set()`来设置变量

#### 1.4 定义集合变量

在新建和编辑集合的时候，可以在 **Variables** 标签页里定义集合变量。

#### 1.5 导入数据文件的变量

在使用 **Runner** 运行集合的时候，你可以导入 CSV 或者 JSON 数据文件，然后使用这些数据发送 HTTP 请求，这些数据的会根据字段名称导入为变量。这些变量在请求中一样可以使用`{{variableName}}`来插入，在脚本中可以使用`pm.iterationData.get("username")`来获取，例如：  
![](https://img-blog.csdnimg.cn/20190727112232733.png)

#### 1.6 内置变量

Postman 提供了几个内置变量来加强请求，你可以在请求的 URL、请求头或请求体等地方通过`{{...}}`来使用，这些变量包括：

*   `{{$guid}}`：生成字符串 UUID
*   `{{$timestamp}}`：生成当前时间的时间戳
*   `{{$randomInt}}`：生成 0 到 1000 的随机整数

> 点击界面右上角的**眼睛**图标可以快速查看当前的环境变量和全局变量。

### 2. 环境和全局变量

当测试 API 的时候，你可能需要在本地环境、开发环境、测试环境或者生产环境使用不同的变量设置，使用环境变量可以很容易的实现这一功能。

#### 2.1 创建环境和全局变量

点击界面右上方的 **Manage Environments** 图标来管理环境变量和全局变量。  
![](https://img-blog.csdnimg.cn/20190727112300429.png)  
点击 **Add** 按钮添加环境变量，点击 **Globals** 按钮添加全局变量。

#### 2.2 选择环境

点击界面右上方的下拉列表框可以选择环境，选择不同的环境激活不同的变量组。  
![](https://img-blog.csdnimg.cn/20190727112337120.png)

#### 2.3 查看环境和全局变量

点击界面右上方的 “眼睛” 图标，可以快速浏览当前的环境变量和全局变量，点击 **Edit** 可以编辑。  
![](https://img-blog.csdnimg.cn/20190727112410256.png)

### 3. 作用域和优先级

下图描述了 Postman 中变量的工作方式：  
![](https://img-blog.csdnimg.cn/20190727112455940.png)  
变量使用示例：假设我们创建了三个集合 C1、C2 和 C3，还创建了三个环境 Dev、Staging 和 Prod，在三个环境中都存在一个 URL 变量，在不同的环境有不同的值。每个集合中有一个定义超时的变量，集合 C1 中的是 10，C2 中的是 20，C3 中的是 30。定义一个全局变量重试次数为 3，如下图所示。  
![](https://img-blog.csdnimg.cn/20190727112534847.png)  
现在，选择不同的环境就可以使用不同的 URL，每个集合下的超时时间是不一样的，但与哪个环境无关，全局变量不管在哪个环境，哪个集合下都是一样的。

六、运行
----

集合是一组请求，它可以针对不同环境运行，当运行集合或者文件夹的时候，这一组请求有序地运行，集合运行对于自动化测试 API 是非常有用的，你可以按照业务逻辑顺序一个接一个的执行请求，结合脚本形成的请求工作流更是提高了接口测试的机动性。

### 1. 运行集合

点击左上角的 **Runner** 按钮开启集合或文件夹运行。  
![](https://img-blog.csdnimg.cn/20190727112555423.png)  
选择相应的集合或者文件夹，并配置好运行规则，就可以批量运行请求了。  
![](https://img-blog.csdnimg.cn/20190727112618885.png)

### 2. 导入数据

在运行集合的时候可以导入外部数据文件，文件格式可以是 `json` 也可以是 `CSV` 。这可以方便的导入更多的测试用例，增加测试的 case 覆盖度。  
![](https://img-blog.csdnimg.cn/20190727112641903.png)  
导入 `CSV` 数据格式：

```
 path, value
 post, 1
 post, 2
 post, 3
 post, 4

```

### 3. 迭代运行

在集合运行的时候，可以配置迭代次数，也就是一个集合或文件夹重复请求多少次。请求结果会统一处理，请求完成后查看结果如下，可以点击 **Run Summary** 查看结果统计。  
![](https://img-blog.csdnimg.cn/20190727112926322.png)

### 4. 工作流

当你在运行一个集合的时候，所有的请求都是按照集合中请求列表的顺序执行的，如果你需要按照自己的执行顺序，你可以使用 `postman.setNextRequest()` 来指定当前请求执行的下一个请求。使用该函数，将你的请求按照业务顺序串联起来，就会形成一个请求工作流，在自动化测试中非常有用。

### 5. 命令行

Newman 是 Postman 的集合运行命令行工具，可以使用它直接使用命令行运行 Postman 请求集合，你可以很简单的集成到你的持续集成服务器中，比如：Jenkins。  
![](https://img-blog.csdnimg.cn/20190727112957381.gif)  
Newman 使用 Node.js 构建，所以在安装使用之前，你需要在电脑上搭建 Node 环境，然后安装并使用：

```
# 安装 Newman
$ npm install -g newman

# 运行集合
$ newman run mycollection.json

# 查看命令帮助
$ newman run -h

```

你也可以在你的 Node 项目中使用 Newman，只需安装后在代码中引入包即可：

```
var newman = require('newman'); // require newman in your project

// call newman.run to pass `options` object and wait for callback
newman.run({
    collection: require('./sample-collection.json'),
    reporters: 'cli'
}, function (err) {
	if (err) { throw err; }
    console.log('collection run complete!');
});

```

七、文档
----

Postman 支持请求接口生成 API 文档的功能，以便管理各个分类的接口，不过只支持在浏览器中查看。在集合的详情栏中点击 **View in web** 就可以实时查看该集合的接口文档。  
![](https://img-blog.csdnimg.cn/20190727113021697.png)

八、Mock
------

Postman 支持 Mock 服务功能，这在前端和后端协同开发的时候，并不需要前端开发人员等后端开发人员接口开发完成才能进行对接，可以实现前后端并行开发，可大大地降低开发延迟，这和别的 Mock 服务一样，只需要维护请求接口和接口响应示例。

创建一个 Mock 服务的步骤如下：

1.  发送一个请求（R1）
2.  将请求（R1）保存到一个集合中（C1）
3.  保存请求（R1）的响应作为示例（P1）
4.  为集合（C1）创建一个 Mock 服务器（M1）
5.  向 Mock 服务器（M1）发送请求
6.  使用 URL 查询变量匹配请求

在创建 Mock 服务的时候要注意，如何维护接口的响应示例，因为别人请求 Mock 服务器的时候，返回正确的响应示例是很重要的，而 Postman 中对 Mock 接口的响应是通过 **Examples** 来维护的，可以直接点击 右上方的 **Examples** 来创建响应示例，也可以保存当前请求的响应创建来创建示例，点击 **Save Response | Save as example**。  
![](https://img-blog.csdnimg.cn/20190727113102911.png)  
在集合中创建好了请求和示例后，就可以在集合的详情页面创建 Mock 服务器了，创建好后会生成一个服务器 URL，URL 的格式为：`https://{{mockId}}.mock.pstmn.io`，然后就可以请求 Mock 服务的接口了，例如请求路径为 `/get` 的接口：

```
https://b75a340e-4268-4b20-8f5f-3cfc8f37cec6.mock.pstmn.io/get

```

![](https://img-blog.csdnimg.cn/20190727113127442.png)

九、工作空间
------

在使用 Postman 的时候，为了分开存放每个项目的请求集合，比如自己私人项目和公司项目，可以创建多个工作空间，每个工作空间的所有配置都是独立存在的，它们之间互不影响。Postman 支持私人工作空间和团队工作空间，这两种类型的操作都一样，唯一不同的是团队工作空间可以有多个开发人员维护，在团队协作上功能更强大。点击界面上方的 **Workspace** 就可以配置和切换工作空间。  
![](https://img-blog.csdnimg.cn/20190727113147857.png)

十、实践
----

在前面的学习当中，我们掌握了 Postman 的大部分操作，下面讲解在实际工作当中怎样使用 Postman 是最高效的。我们会基于 Postman 的工作空间、环境变量、脚本等功能来完成接口的请求和测试。

**使用不同的工作空间**

上一节已经说过针对不同的项目创建不同的工作空间，分类维护请求集合。

**使用不同的环境**

对实际项目中的环境抽象，这些环境中的变量针对所有的集合有效，可以配置基于环境的变量，例如：

*   每个系统的服务器地址（域名或 IP 地址）

除了上面的配置外，还有别的基于环境的配置都可以在环境变量中配置。

不同的环境配置不同的 host 地址，可以是域名或者 IP 地址：  
![](https://img-blog.csdnimg.cn/20190727113439824.png)  
**不同的系统使用不同的集合**

每个系统使用独立的集合，可以维护针对集合的变量和脚本，例如：

*   每个系统的接口权限校验方式

*   每个系统的接口返回成功测试脚本

*   每个系统的服务器端口号

除了上面的之外，还有别的基于系统的配置都可以转换成针对某个请求集合的配置。  
![](https://img-blog.csdnimg.cn/20190727113457728.png)  
在某个系统下的全局测试脚本可以在集合中定义，比如接口是否请求成功测试：  
![](https://img-blog.csdnimg.cn/20190727113518932.png)  
在某个系统作用域下的变量，也就是只有该系统下的请求才使用的变量可以在集合中定义，比如该系统的服务器端口号：  
![](https://img-blog.csdnimg.cn/20190727113539229.png)  
**系统中不同类型的接口使用不同的文件夹**

在一个系统中，每个流程的接口，或者某个类型的接口应该创建独立的文件夹，因为在创建工作流进行自动化的时候，可以直接运行该文件夹。  
![](https://img-blog.csdnimg.cn/20190727113557309.png)  
**请求中使用变量**

上面我们基于环境和基于集合都创建了变量，在请求的构建中，应该使用变量，比如前面定义的服务器地址和端口号，这样设计的好处就是，我们可以在不同的系统中都可以使用 `{{host}}:{{port}}` 的形式来配置接口的服务器根路径，然后针对不同的环境进行请求，只需切换环境而不需要对请求进行修改。  
![](https://img-blog.csdnimg.cn/20190727113623260.png)  
**使用预置请求头**

如果是多个请求的请求头都一样，可以预先配置好请求头，然后直接使用。  
![](https://img-blog.csdnimg.cn/20190727113650445.png)  
**从登录接口中获取 Token**

如果接口的权限设计是通过 Token 来实现的，可以在登录的请求中添加测试脚本，把登录成功后返回的 Token 值配置成全局变量，以便后续的请求使用，而不用手动去复制粘贴。  
![](https://img-blog.csdnimg.cn/20190727113710291.png)  
可能还有更多的高效操作方法，可以一起研究，对工具的熟练程度影响着你的开发效率。

