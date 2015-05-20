## CSS编码规范 ##

以下规范是最近几个项目的总结，不断更新中。。

### 浏览器特效支持规范 ###

为了页面性能考虑，如果浏览器不支持CSS3相关属性的，则该浏览器的某些特效将不再支持，属性的支持情况如下表所示：
![](http://i.imgur.com/GZWhWCo.png)


### 文件相关规范 ###

+ 文件名必须由小写字母、数字、下划线_组成（结合下面的书写规范）
+ 文件必须用utf-8编码
+ 文件引入必须通过外联`<link rel=”stylesheet” href="…” />`（类型声明type=”text/css”不可以忽略）
+ 不允许在html上直接写样式
+ link标签都应该放入head中

### 注释规范 ###

+ 在对CSS代码进行注释时，使用//和<!-- -->进行注释，其中//进行单行注释时使用，<!-- -->进行多行注释时使用。

### 排版规范

+ 一个tab设置为四个空格宽度。
+ CSS定义规则可以写成单行，或者多行，但是整个文件内的规则排版必须统一。
+ 使用单引号，不允许使用双引号(html标签的属性使用双引号，不允许使用单引号)
+ 如果使用CSS3的属性，如果有必要加入浏览器前缀，则按照 (-webkit- / -moz- / -ms- / -o- / std)的顺序进行添加，标准属性写在最后，并且属性名称要对齐

### 规则书写规范

+ 除16进制颜色和字体设置外，CSS文件中的所有的代码都应该小写。
+ 规则命名中，，一律采用中划线-或者驼峰式命名，禁止采用小写加下划线的方式
+ 命名不允许使用中文拼音，应该采用更简明有语义的英文单词进行组合(部分使用英文简写)
+ 避免class与id重名
+ class用于标识某一个类型的对象，命名必须言简意赅。
+ 尽可能提高代码模块的复用，样式尽量用组合的方式
+ 除了重置浏览器默认样式外，禁止直接为html tag添加css样式设置
+ 微信端的页面宽度要用百分比的形式（切记！！！）

### 推荐的样式编写顺序

+ 显示属性，例如：
display/list-style/position/float/clear
+ 自身属性（盒模型）
width/height/margin/padding/border
+ 背景
background
+ 行高
line-height
+ 文本属性
color/font/text-decoration/text-align/
text-indent/vertical-align/white-space/content
+ 其他
cursor/z-index/zoom
+ CSS3属性
transform/transition/animation/box-shadow/border-radius
+ 链接的样式请严格按照如下顺序添加：
a:link -> a:visited -> a:hover -> a:active（LoVeHAte）

### 性能优化

+ 合并margin、padding、border的-left/-top/-right/-bottom的设置，尽量使用短名称。
+ 选择器应该在满足功能的基础上尽量简短，减少选择器嵌套，查询消耗。但是一定要避免覆盖全局样式设置。
+ 注意选择器的性能，不要使用低性能的选择器，例如：
	- h1 { color: black; }
	- div > * {}    
	- ul > li > a {}
	- body.profile ul.tabs.nav li a {}

+ 禁止在css中使用*选择符(除了浏览器初始化时候)
+ 除非必须，否则，一般有class或id的，不需要再写上元素对应的tag，例如：div#test { width: 100px; }
+ 0后面不需要单位，比如0px可以省略成0，0.8px可以省略成.8px
+ 如果是16进制表示颜色，则颜色取值应该尽量大写。
+ 如果没有边框时，不要写成border:0，应该写成border:none
+ 在保持代码解耦的前提下，尽量合并重复的样式，例如：
	- h1 { color: black; }
	- p { color: black; }
	- h1, 
	- p { color: black; }
	- background、font等可以缩写的属性，尽量使用缩写形式
	- background: color image repeat attachment position;
	- font: style weight size/lineHeight family;

### hack使用规范

+ 重要原则：尽量少用hack，能不hack坚决不hack，不允许滥用hack。
+ 如果需要使用hack，请参考以下hack方式：[https://gist.github.com/q946401639/4e8a0e969430ca387c55](https://gist.github.com/q946401639/4e8a0e969430ca387c55)
### 常用的CSS命名

+ 头		header	
+ 内容 	content	
+ 尾	 	footer	
+ 导航	nav
+ 子导航	subnav	
+ 栏目 	column	
+ 主体 	main	
+ 新闻	news
+ 版权	copyright	
+ 文章列表	list	
+ 加入	joinus	
+ 合作伙伴	partner
+ 标志	logo	
+ 侧栏	sidebar	
+ 横幅	banner	
+ 状态	status
+ 菜单	menu	
+ 子菜单	submenu	
+ 滚动	scroll	
+ 搜索	search
+ 标签页	tab	
+ 提示信息	msg
+ 小技巧	tips	
+ 标题	title
+ 指南	guide	
+ 服务	service	
+ 热点	hot	
+ 下载	download
+ 注册	register	
+ 登录条	loginbar	
+ 按钮	btn	
+ 投票	vote
+ 注释	note	
+ 友情链接	friend-link	
+ 外套	wrap	
+ 面包屑	bread-crumb
+ 当前的	current	
+ 购物车	shop	
+ 图标	icon	
+ 文本	txt

待补充...

### 其他细节 ##

+ 字体名称请映射成对应的英文名，例如：黑体(SimHei) 宋体(SimSun) 微软雅黑 (Microsoft Yahei)，如果字体名称中有空格，则必须加单引号。(使用字体注意测试所有浏览器，并且注意mac本上的浏览器测试)
+ 背景图片请合理使用csssprites，按照模块、业务、页面来划分均可
+ css背景图片的文件类型，请按照以下原则来保存：
+ 如果背景图片有动画，则保存成gif
+ 如果没有动画，也没有半透明效果，则保存成png-8或JPEG
+ 如果有半透明效果，则保存成png-24
+ 不要在html中加入标签来清理浮动，通过在浮动元素的父元素上添加.clearfix来清除浮动
+ 尽量少用!important
+ ID必须唯一，并且要在结构的定义中。
+ CSS中不使用expression。
+ H标签中H1标签每个页面允许使用一次。
+ html中图片命名规则
+ 名称分为头尾两两部分，用下划线隔开。
+ 头部分表示此图片的大类性质。例如： 放置在页面顶部的广告、装饰图案等长方形的图片我们取名：banner ；标志性的图片我们取名为：logo 。
+ 尾部分用来表示图片的具体含义，用英文字母表示。例如：title_news.gif.
+ 有onmouse效果的图片，两张分别在原有文件名后加”_on”和”_off”命名