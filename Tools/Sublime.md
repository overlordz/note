# Sublime



Sublime Text具有漂亮的用户界面和强大的功能，并且是一个跨平台的编辑器，同时支持[Windows](https://baike.baidu.com/item/Windows)、[Linux](https://baike.baidu.com/item/Linux)、[Mac OS X](https://baike.baidu.com/item/Mac%20OS%20X)等操作系统。

登录官网下载 https://www.sublimetext.com/

## Package Control

> 插件网站：https://packagecontrol.io/

**操作步骤**

1. 以下内容基于已经你已经在你的SublimeText中安装了package control
2. 通过快捷键组合ctrl+shift+P唤出命令面板
3. 在面板中输入“install package”后回车
4. 接着输入插件名称如：“emmet” 回车即可（观察状态栏进度，晃动的等号）



## 风格

风格包网站:[ https://packagecontrol.io](https://packagecontrol.io/)

风格包: Spacegray：

```
https://packagecontrol.io/packages/Theme%20-%20Spacegray
```

安装主题

1. Package Control: Install Package
2. 找到`Theme - Spacegray` 并选择

设置主题

1. Preferences > color scheme >theme-spacegray
2. Preferences-> setting 中设置以下

```
"theme": "Spacegray.sublime-theme",
"color_scheme": "Packages/Theme - Spacegray/base16-ocean.dark.tmTheme"
```



## 图标

包地址：https://github.com/ihodev/a-file-icon

安装方式：

1. Open `Command Palette` using menu item `Tools → Command Palette...`
2. Choose `Package Control: Install Package`
3. Find `A File Icon` and hit `Enter`



## 常用插件

- [Emmet](https://docs.emmet.io)：快速编写html/css
- [IMESupport](https://packagecontrol.io/packages/IMESupport)：解决输入框不跟随的问题
- [BracketHighlighter](https://packagecontrol.io/packages/BracketHighlighter)：高亮显示配对括号以及当前光标所在区域
- [docblockr](https://packagecontrol.io/packages/DocBlockr)：代码注释提示插件 
- [sideBarEnhancements](https://packagecontrol.io/packages/SideBarEnhancements) ：扩展左侧面板
- [SideBarTools](https://packagecontrol.io/packages/SideBarTools)：扩展左侧面板 
- [AdvancedNewFile](https://packagecontrol.io/packages/AdvancedNewFile)：快速创建文件 ，使用ctrl+alt+n就可以快速创建文件
- [Local History](https://packagecontrol.io/packages/Local%20History)：本地历史记录 
- [Laravel 5 Artisan](https://packagecontrol.io/packages/Laravel%205%20Artisan )：laravel命令行插件 ，按 ctrol+shift+p 搜索并执行命令
- [Laravel 5 Snippets](https://packagecontrol.io/packages/Laravel%205%20Snippets)：laravel代码片段 
- [Laravel Blade Highlighter]( https://packagecontrol.io/packages/Laravel%20Blade%20Highlighter)：laravel blade模板高亮
- [Blade Snippets](https://packagecontrol.io/packages/Blade%20Snippets)：blade 代码片段 
- [Laravel Blade AutoComplete](https://packagecontrol.io/packages/Laravel%20Blade%20AutoComplete)：自动识别blade父级模板内容
- [Bootstrap 3 Snippets](https://github.com/JasonMortonNZ/bs3-sublime-plugin)：bootstrap 代码片段 
- [Bootstrap 3 Autocomplete](https://packagecontrol.io/packages/Bootstrap%203%20Autocomplete)：bootstrap样式提示 
- [Bootstrap 4 Snippets](https://packagecontrol.io/packages/Bootstrap%204%20Snippets)：bootstrap代码提示
- [Bootstrap 4 Autocomplete](https://packagecontrol.io/packages/Bootstrap%204%20Autocomplete)：bootstrap4 自动提示
- [SCSS](https://packagecontrol.io/packages/SCSS)：scss代码提示
- [SCSS](https://packagecontrol.io/packages/Sass)：scss语法高亮 
- [html-css-js prettify](https://packagecontrol.io/packages/HTML-CSS-JS%20Prettify)：一键格式化html/css/js（依赖nodejs）
- [CTags](https://packagecontrol.io/packages/CTags)：按住Control点击该方法或者对象，即可跳转到定义的地方
- [DeleteBlankLines](https://packagecontrol.io/packages/DeleteBlankLines)：快速删除空白行
- [SublimeGit](https://packagecontrol.io/packages/SublimeGit)：git
- [SublimeTmpl](https://packagecontrol.io/packages/SublimeTmpl)： 快速生成文件模板
- [ConvertToUTF8](https://packagecontrol.io/packages/ConvertToUTF8)： 支持 GBK, BIG5, EUC-KR, EUC-JP, Shift_JIS 等编码的插件



## LESS

安装 node [https://nodejs.org/zh-cn/](https://nodejs.org/zh-cn/)

**编译环境**

```
npm install -g less@2.7.3
npm install -g less-plugin-clean-css
npm install -g less-plugin-autoprefix

```

> mac&linux 系统使用sudu安装

设置好之后，在命令行中输入lessc，如果有如下提示则表示设置成功：

```
C:\Users\koala>lessc
lessc.wsf: no input files
Usage:
Single file: cscript //nologo lessc.wsf input.less [output.css] [-compre
Directory:   cscript //nologo lessc.wsf inputdir outputdir [-compress]
```

**插件**

sublime需要安装两个插件一个是编译less文件，一个是语法高亮

1. https://packagecontrol.io/packages/Less2Css
2. https://packagecontrol.io/packages/LESS

这时可以在Sublime Text 中打开或者新建一个less文件，按下Ctrl+S保存，此时应该会在less文件的相同目录下生成同名的css文件。

## 格式化

首先通过以下路径打开用户按键绑定文件：

Preferences → Key Bindings – User

然后在其中添加以下代码（如果你有需要的话，其中的快捷键组合是可以自己定义的）：

```
[
	//格式化代码,single_line参数删除时，格式化只影响当前光标所在行
	{"keys": ["ctrl+alt+l"], "command": "reindent" , "args": {"single_line": false}}
]
```



# 函数

#### 快速查找

默认情况下，Sublime Text支持函数快速查找，按Ctrl+Shift+R打开查找面板，就可以快速定位函数所在的文件，如果安装了emmet插件将会失效，我们需要做以下操作进行修复。

编辑 emmet插件配置项：

```
{"disabled_keymap_actions": "reflect_css_value"}
```

#### 函数跟踪

鼠标移动到函数上面，会自动显示方法的文件列表。或按 f12 键显示函数的文件列表



## 快捷键

#### [选择类]
| 按 Press         | 功能 Function                                                |
| ---------------- | ------------------------------------------------------------ |
| **Ctrl+D**       | 选中	光标所占的文本，继续操作则会选中下一个相同的文本     |
| **Alt+F3**       | 选择	所有相同的词                                         |
| **Ctrl+L**       | 选中	整行，继续操作则继续选择下一行，效果和 Shift+↓ 效果一样 |
| **Ctrl+Shift+L** | 先选中多行，再按下快捷键，会在每行行尾插入光标，即可同时编辑这些行 |
| Ctrl+Shift+M     | 选择括号内的内容（继续选择父括号）举个栗子    快速选中删除函数中的代码，重写函数体代码或重写括号内里的内容 |
| Ctrl+M           | 光标	移动至括号内结束或开始的位置                         |
| Ctrl+Enter       | 在下一行插入新行举个栗子即使光标不在行尾，也能快速向下插入一行 |
| Ctrl+Shift+Enter | 在上一行插入新行举个栗子即使光标不在行首，也能快速向上插入一行 |
| Ctrl+Shift+[     | 选中代码，按下快捷键，折叠代码                               |
| Ctrl+Shift+]     | 选中代码，按下快捷键，展开代码                               |
| Ctrl+K+0         | 展开所有折叠代码                                             |
| Ctrl+←           | 向左单位性地移动光标，快速移动光标                           |
| Ctrl+→           | 向右	单位性地移动光标，快速移动光标                       |
| shift+↑          | 向上	选中多行                                             |
| shift+↓          | 向下	选中多行                                             |
| Shift+←          | 向左	选中文本                                             |
| Shift+→          | 向右	选中文本                                             |
| Ctrl+Shift+←     | 向左单位性地选中文本                                         |
| Ctrl+Shift+→     | 向右单位性地选中文本                                         |
| Ctrl+Shift+↑     | 将光标所在行和上一行代码互换（将光标所在行插入到上一行之前） |
| Ctrl+Shift+↓     | 将光标所在行和下一行代码互换（将光标所在行插入到下一行之后） |
| Ctrl+Alt+↑       | 向上添加多行光标，可同时编辑多行                             |
| Ctrl+Alt+↓       | 向下添加多行光标，可同时编辑多行                             |



#### [编辑类]

| 按 Press               | 功能 Function  	|
| ---------------------- | ----------------------------------- |
| **Ctrl+J** | 	合并选中的多行代码为一行 |
| **Ctrl+Shift+D** | 	复制光标所在整行，插入到下一行 |
| Tab 				| 	向右缩进 |
| Shift+Tab 		| 	向左缩进 |
| **Ctrl+K+K** | 	从光标处开始删除代码至行尾 |
| **Ctrl+Shift+K** | 	删除整行 |
| Ctrl+X    		| 	删除当前行 |
| Ctrl+/ 			| 	注释单行 |
| Ctrl+Alt+/    	| 	块注释，并Focus到首行，写注释说明用的 |
| Ctrl+Shift+/ 		| 	注释多行 |
| Ctrl+K+U 			| 	转换大写 |
| Ctrl+K+L 			| 	转换小写 |
| Ctrl+Z 			| 	撤销 |
| Ctrl+Y 			| 	恢复撤销 |
| Ctrl+U 			| 	软撤销，感觉和 Gtrl+Z 一样 |
| Ctrl+F2 			| 	设置书签 |
| Ctrl+T 			| 	左右字母互换 |
| F6 				| 	单词检测拼写 |
| Ctrl+Shift+F    	| 	查找并替换 |
| Ctrl+H    		| 	替换 |
| Alt+.    			| 	闭合标签 |
| Ctrl+Shift+A    	| 	选择当前标签前后，修改标签用的 |
| Ctrl+Shift+↑/↓  	| 	可替换行 |



#### [搜索类】

| 按 Press     | 功能 Function  |
| ------------ | -------------- |
| Ctrl+F       | 搜索查找关键字 |
| Ctrl+shift+F | 在文件夹内查找 |
| **Ctrl+P** |	快速搜索文件 |
| -- |	@ 符号跳转：查找文件中函数名 |
| -- |	#关键字跳转：查找变量名 |
| -- |	：行号跳转：跳转到第N行 |
| **Ctrl+G** |	跳转到某个行数 |
| **Ctrl+R** |	查找文件中的函数名 |
| Ctrl+     	 |	查找文件中的变量名、属性名等 |
| Ctrl+Shift+P 	 |	打开命令面板 |
| Esc 			 |	退出光标多行选择，退出搜索框，命令框等 |



#### 显示类

| 按 Press      | 功能 Function                            |
| ------------- | ---------------------------------------- |
| **Ctrl+W**    | 关闭当前打开文件                         |
| Ctrl+Shift+W  | 关闭所有打开文件                         |
| Ctrl+Shift+V  | 粘贴并格式化                             |
| Ctrl+Tab      | 按文件浏览过的顺序，切换当前窗口的标签页 |
| Ctrl+PageDown | 向左切换当前窗口的标签页                 |
| Ctrl+PageUp   | 向右切换当前窗口的标签页                 |
| Alt+Shift+1   | 窗口分屏，恢复默认1屏（非小键盘的数字）  |
| Alt+Shift+2   | 左右分屏-2列                             |
| Alt+Shift+3   | 左右分屏-3列                             |
| Alt+Shift+4   | 左右分屏-4列                             |
| Alt+Shift+5   | 等分4屏                                  |
| Alt+Shift+8   | 垂直分屏-2屏                             |
| Alt+Shift+9   | 垂直分屏-3屏                             |
| Ctrl+K+B      | 开启/关闭侧边栏                          |
| alt+-         | 上一个编辑处                             |
| **Alt+数字**  | 切换打开第N个文件                        |
| alt+shift+-   | 下一个编辑处                             |
| Ctrl+N        | 新建窗口                                 |
| F11           | 全屏模式                                 |
| Shift+F11     | 免打扰模式                               |



## 用户设置（首选项—>设置-用户）

- 消除升级提示："update_check":false,
- 设置默认编码为UTF-8："default_encoding": "UTF-8",
- 设置字体:  "font_face": "Consolas",
- 行号边栏和文字的间距:  "margin": 4,
- 自动匹配引号，括号等:  "auto_match_enabled": true,
- 突出显示当前光标所在的行:  "highlight_line": false,
- 设置行距: "line_padding_top": 1,  "line_padding_bottom": 1,
- 代码提示:  "auto_complete": true,
- 保持退出前的状态:  "hot_exit": true,
- 解决中文文件名显示问题："dpi_scale": 1.0,

个人配置

```
{
	"auto_match_enabled": true,
	"color_scheme": "Packages/Theme - Spacegray/base16-ocean.dark.tmTheme",
	"default_encoding": "UTF-8",
	"font_size": 14,
	"highlight_line": true,
	"ignored_packages":
	[
		"Vintage"
	],
	"line_padding_bottom": 2,
	"line_padding_top": 2,
	"theme": "Spacegray.sublime-theme",
	"update_check": false
}
```





## 链接（Links）

- 官方文档：<http://www.sublimetext.com/docs/3/>
- 官方论坛：<http://www.sublimetext.com/forum/>
- Stack Overflow 的 Sublime Text 频道：
  - http://stackoverflow.com/questions/tagged/sublimetext
  - http://stackoverflow.com/questions/tagged/sublimetext2
  - http://stackoverflow.com/questions/tagged/sublimetext3
- 非官方文档：http://sublime-text-unofficial-documentation.readthedocs.org/  甚至比官方文档还要全面！
- Package Control：https://packagecontrol.io/  大量的 Sublime Text 插件和主题。



#### 同类网址：

http://zh.lucida.me/blog/sublime-text-complete-guide/

https://www.jianshu.com/p/d1b9a64e2e37

https://juejin.im/post/584f53228d6d8100545abc55



























# LNPA3GBC5BG028783

