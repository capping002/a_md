# Symfony2
	----洪大师视频学习笔记

## 简介

1. Symfony是什么意思？
	
	来源于单词 Symphony: 交响乐
	像交响乐优雅却又严谨，复杂却又灵动的php框架

2. Symfony1和Symfony2

	1.X : 2005 -- 2012
	2.X : 2011 -- LTS 2.3
				  非LTS 2.5
		是对1.X的重写，基本重写核心模块
		遵循PSR-0自动载入标准
		遵循比较新的web开发最佳实践
		2.1版本开始启用composer第三方包管理工具

	查阅： PSR-0自动载入标准

3. 为什么要使用Symfony2

	a. 快速开发健壮的，可持续升级的网站应用
		- 网站发展初期--快速上线
		- 网站发展中期--频繁的需求变化，功能调整
		- 网站发展后期--在不动代码的情况基础上进行性能优化
	b. 使开发过程更加关注业务逻辑
	c. 通过使用symfony2,更加深入理解并应用网站开发领域中的最佳实践
		例：- 管理css,js
			- 战略部署  管理模板
			- 服务的统一管理

4. Symfony2和其他框架的区别
	
	- 开放性
	- 可扩展性
	- 持续升级性
	- 有商业背景和支持性
	- 被广泛使用性
	- 引领php领域最佳实践性

5. Symfony2速度怎么样？
	
	如何衡量一个框架的“速度”？
		这个框架提供的对于性能优化的功能完善和强大的角度考虑

	影响网站速度到底有哪些?
	 静态化   缓存 :Annotation    数据操作合并处理

**如何使用Symfony2开发网站应用**

	- 如何搭建开发环境
	- 使用Symfony2开发一个招聘网站
	- 如何进行网站的性能优化(js, css, 缓存, 静态化)
	- 如何优雅的部署代码
		[
			1. 第一次上线：初始化 环境设置  布置规则设定
			2. 第二步网站更新：数据处理  旧数据转义  兼容性问题
		]

## 学习资料
	- symfony官网：https://symfony.com/download
	- stack overflow：https://stackoverflow.com/
	- github: https://github.com/symfony/symfony

## 开发环境搭建
	
	1. 使用PHP CLI-Server 搭建简易开发环境
		> php app/console server:run
	2. 使用Vagrant搭建完整开发环境
	3. 搭建共享开发服务器

**这一部分还需要以后的进一步学习**

## Symfony2的核心文件及目录结构介绍

1. 安装composer包管理工具
2. 安装Symfony2
	install the Symfony Installer

	Then execute this command
	$ symfony new my_project 2.8

**遇到的问题1**

	cURL error 60: SSL certificate problem: unable to get local issuer certificate
	解决：https://stackoverflow.com/questions/29822686/curl-error-60-ssl-certificate-unable-to-get-local-issuer-certificate
	
	a) 大致就是下载一个cacert.pem到D:\PHPSoft\phpStudy\php\php-7.0.12-nts\ext目录
	b) 然后在php.ini中添加一行：curl_cainfo = "D:\PHPSoft\phpStudy\php\php-7.0.12-nts\ext\cacert.pem"

**遇到的问题2**
	FAILED  Symfony 2.8.18 was successfully installed but your system doesn't meet its
     technical requirements! Fix the following issues before executing
     your Symfony application:

  	date.timezone setting must be set
   	> Set the "date.timezone" setting in php.ini* (like Europe/Paris).
   	解决：设置timezone = PRC

***
**解决以上问题后可以看到**
	 After fixing these issues, re-check Symfony requirements executing this command:

   	$ php local.symfony.com/app/check.php

   	* Change your current directory to E:\vhost\local.symfony.com

    * Configure your application in app/config/parameters.yml file.

    * Run your application:
        1. Execute the php app/console server:run command.
        2. Browse to the http://localhost:8000 URL.

    * Read the documentation at http://symfony.com/doc
**另一些东西**
	Optional recommendations to improve your setup
	可选的建议，以改善你的设置
	~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
	intl扩展应该可用
	 * intl extension should be available
	   > Install and enable the intl extension (used for validators).
	
	应该安装一个php加速器
	 * a PHP accelerator should be installed
	   > Install and/or enable a PHP accelerator (highly recommended).

	在php.ini中，realpath_cache_size应该至少为5M
	 * realpath_cache_size should be at least 5M in php.ini
	   > Setting "realpath_cache_size" to e.g. "5242880" or "5M" in
	   > php.ini* may improve performance on Windows significantly in some
	   > cases.

	
3. 安装Symfony的各个依赖组件

#### Symfony2的核心文件及目录结构介绍

	1. composer.json: composer的一个定义文件，定义这套程序中会用到的第三方的代码
		（定义->composer update->vender）
		symfony的核心组件也是用composer.json定义的

	2. app/appkernel: 如果在composer.json中安装了某个第三方的包，要在appkernel中进行启用
	   app/cache: 核心文件的缓存，类的映射关系，annotations缓存文件
	   app/config: 各种配置文件

	3. bin: 放一些执行文件

	4. src-AppBundle
			-Controller
			-Dependencyinjection
			-EvenListener
			-Form
			-Resources
			-Tests
			-Twig

	5. vendor

	6. web
		app.php: 单一的入口文件

#### 编写一个hello world页面
	1. 创建一个Bundle
		> app/console generate:bundle
	  创建完成后在app/AppKenel.php自动帮我们完成了注册，同时在config/routing中帮我们添加了参数

	2. 控制器中的注释：Route定义了路由规则;
					   Template()定义了模板，()中可以加参数的;**还不会用**
					   Method():使用method之前要
					   ``` use Sensio```
```
	/**
     * @Route("/hi")
     * @Method("POST")
     * @Template()
     */
```
	**以后要了解一下getRequest和getMethod** 	
	+ getRequest() : 得到用户提交的所有信息


## 路由

#### 路由工作原理及配置

1. 概念：URL和程序方法的一一映射，
	在开发较复杂的网站时，可以将常访问的路由放在前面，可以稍稍提高网站的访问速度9

2. Symfony2配置路由的方式

	+ Annotation
		允许你在方法的上面用注释定义方法运行状态的功能
	+ router.yml
		Symfony2常用的配置格式
	+ router.xml (不常用)
	+ PHP (不常用)

3. 如何定义URL
	
	+ 静态URL
	+ 动态URL
		验证：defaults:默认值;  requirements:正则匹配;
			* @Route("/page/{page_num}", name="page_index" defaults={"page_num":1}, requirements={"page_num"="\d+"})

**如何快速的查找页面对应的URL？**

	a) 使用Symfony的调控工具
	b) > app/console router:debug  //打印系统中所有的路由
	   > app/console debug:router
	 !
	 ! [CAUTION] The use of "router:debug" command is deprecated since version 2.7
	 !           and will be removed in 3.0. Use the "debug:router" instead.
	 !
	 **router:debug是在2.7之前的版本使用的，并且将在3.0版本移除，使用debug:router代替**

	   路由匹配功能 > app/console router:match /page/index
	   路由匹配功能没有实现，以后解决

## Controller简介
	Symfony是对Request进行加工，根据业务需求处理成特定的Response并返回给用户的一个流程，
而Request和Response都是Symfony中的类

1. Controller之Request
	
	- $this->getRequest()->isXmlHttpRequest();  //是否是Ajax请求
	- $this->getRequest()->get();				//可以获取到get或者post中的参数

**扩展：https://github.com/jakubroztocil/httpie**
	可以用来在命令行模拟http请求

2. Controller之Response

	> return new Response("12345");	
	> return new JsonResponse(array('name'=>'zhang'));	// 返回一个JSON字符串
	> return new RedirectResponse(string $url, int $status = 302, array $headers = array());

3. Controller之Session

	> $this->getRequest()->getSession()->set('age', 19);	//设置session
	> $this->getRequest()->getSession()->get('age');		//获取session
	以下方式用在表单的验证中较多
	> $this->getRequest()->getSession()->getFlashBag()->add(
		'notice',
		'you have something wrong'
	);

**如果设置session后取出来的值是null，可能是因为app/cache/dev目录下的权限设置不对**

4. Controller之Service

+ 什么是服务？
	程序架构的方式
	作用：把特定事情的类或方法独立于当前代码单独存在并通过Service进行调用

+ SOA ： Service Oriented Architecture 面向服务的架构

+ 显示所有的service： > app/console container:debug
					  > app.console debug:container
 !
 ! [CAUTION] The use of "container:debug" command is deprecated since version
 !           2.7 and will be removed in 3.0. Use the "debug:container" instead.
 !

## Twig模板引擎
官网：http://twig.sensiolabs.org/
1. 如何使用Twig进行页面的布置和开发？
2. Twig与其他模板引擎的不同

3. 基本语法
	- 输出	{{  }}
	- 判断	{%  %}
	- 注释	{#  #}
4. 核心概念
	用类的继承关系去管理页面之间的关系(extends)

软链接（适用Linux）： 
> php app/console assets:install web --symlink --relative
硬链接（适用于Windows）
> php app/console assets:install web
{{ asset("目录") }}

**定义**
{% block head %}
{% endblock %}

**复写**
例子：
{% extends 'ScourgenWebBundle::layout.html.twig' %}
{% block my_js %}
{% endblock %}
{% block maincontent %}
	内容
	{% block footer %}
		{{ parent() }}
	{% endblock %}
{% endblock %}


## CSS/js文件管理
如何使用Symfony2区管理JS/CSS文件？

1. 网站的资源文件：CSS，JS，图片
2. 为什么要 进行资源文件的管理？
	CSS/JS文件的生命周期：编译-》整合-》混淆-》代码优化最小化-》版本控制
3. 如何把资源文件交给Symfony2进行管理？
（私有的）
	js: 将ScourgenWebBundle/Resources/public/js/文件夹下的所有文件加载下来
	```
	{% block my_js %}
		{% javascripts '@ScourgenWebBundle/Resources/public/js/*' %}
		<script type="text/javascript" src="{{ asset_url }}"></script>
		{% endjavascript %}
	{% endblock %}
	```
	css:将ScourgenWebBundle/Resources/public/css/文件夹下的所有文件加载下来
	```
	{% block my_css %}
		{% stylesheets '@ScourgenWebBundle/Resources/public/css/*' %}
		<link rel="stylesheet" href="{{ asset_url }}" />
		{% endstylesheets %}
	{% endblock %}
	```

4. 页面间共享css/js文件的通用策略
5. Twig模板基于通用策略的调整
（公有的）
css:
```
{% block global_css %}
	{%	stylesheets
		'@ScourgenWebBundle/Resources/public/css/bootstrap.min.css'
		'@ScourgenWebBundle/Resources/public/css/bootstrap-theme.min.css'
		'@ScourgenWebBundle/Resources/public/css/main.css'
	%}
	<link rel="stylesheet" href="{{ asset_url }}">
	{% endstylesheets %}
{% endblock%}
```

js:
```
{% block global_js %}
	{% javascripts
		'@ScourgenWebBundle/Resources/public/js/vendor/respond.js'
	%}
	<script type="text/javascript" src="{{ asset_url }}"></script>
	{% endjavascript %}
{% endblock %}
```

6. 使用coffeescript编写js源文件

7. 代码压缩和混淆
filter="coffee,?uglifujs2"  //加问号代表开发环境不做代码压缩和混淆

8. 对css和js进行版本控制

9. 高级分享--使用多线程进行资源文件dump

## 数组库操作--Doctrine2

1. 使用框架操作数据库的几个基本需求
	+ 安全
	+ 性能
	+ ORM(Object-Relational Mapper)
	+ 支持多种数据库
	+ 支持多种第三方的插件

2. Doctrine2简介
	从2006年成立项目
	代码开源，开发过程透明
	良好的架构
		既能独立开发
		也能结合框架使用
	功能强大，速度快

3. Symfony2为什么选择Doctrine2?
	+ Symfony1.X时代
	+ Symfony2.X时代
	+ 第三方bundle的支持

4. Symfony能不能使用别的数据库操作软件？
	+ 较有名且有社区和官方支持的
		Propel
		Zend DB
	+ 其他的可以自己集成，不复杂，但不推荐








###############################遇到的错误以及解决办法######################################
1. The controller must return a response
	> use Symfony\Component\HttpFoundation\Response;
	  instead of
	> use Symfony\Component\BrowserKit\Response;