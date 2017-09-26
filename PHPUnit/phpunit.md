# PHPUnit

-- 模型工厂  

单元测试 : 针对代码中相对独立而且非常少的一部分代码来进行测试, 实际上, 大多数的单元测试都是针对某一个方法来进行的。

功能测试 : 针对代码中大部分的代码来进行测试, 包括几个对象的相互作用, 甚至是一个完整的HTTP请求实例。

## 测试环境

在运行测试时, laravel会根据phpunit.xml文件中设定好的环境变量自动将环境变量设置成testing, 并将session及缓存以array的
形式存储, 也就是说测试环境不会持久化任何session或缓存信息

你可以随意创建其他必要的测试环境配置。testing环境变量可以在phpunit.xml文件中被修改, 但是在运行测试之前, 请确保config:clear
Artisan命令来清除配置信息的缓存。

## 定义并运行测试

可以是用`make:test`Artisan命令, 创建一个测试用例 :  
```
// 在Feature目录下创建一个测试类
php artisan make:test UserTest

// 在Unit目录下创建一个测试类
php artisan make:test UserTest --unit
```

测试类生成之后，你就可以像平常使用 PHPUnit 一样来定义测试方法。要运行测试只需要在终端上运行 phpunit 命令即可：

```
<?php

namespace Tests\Unit;

use Tests\TestCase;
use Illuminate\Foundation\Testing\DatabaseMigrations;
use Illuminate\Foundation\Testing\DatabaseTransactions;

class ExampleTest extends TestCase
{
    /**
     * 基本的测试用例。
     *
     * @return void
     */
    public function testBasicTest()
    {
        $this->assertTrue(true);
    }
}
```

**如果要在你的测试类自定义自己的`setUp`方法, 请确保调用`parent::setUp()`方法。**

## HTTP测试

Laravel为HTTP请求的生成和输出的检查提供了非常流畅的API

Laravel包含的断言 : 检查HTTP状态码, 检查响应标头, 内容, JSON结构等各种断言。

## Session / 认证

Laravel提供了几个可在测试时使用Session的辅助函数。首先, 你需要传递一个数组给`withSession`方法来设置session数据。这让你的应用程序
的测试请求发送之前, 先给数据加载session变得简单 :  

当然, 一般是用session时都是用于维护用户的状态, 如认证用户。actingAs辅助函数提供了简单的方式来让指定的用户认证为当前的用户。例如, 
我们可以使用模型工厂来生成并认证用户:

```
<?php 

use App\User;

class ExampleTest extends TestCase
{
    public function testApplication()
    {
        $user = factory(User::class)->create();

        $reponse = $this->actingAs($user)
                        ->withSession(['foo' => 'bar'])
                        ->get('/');
    }
}
```

你也可以通过传递guard名称作为actingAs的第二参数以指定用户通过哪种guard来认证 :  
> $this->actingAs($user, 'api');

## Testing JSON APIs

Laravel也提供了几个辅助函数来测试json APIs及其响应。例如 : json, get, post, put, patch和delete方法可以用来发出各种HTTP动作的请求。

```
<?php

class ExampleTest extends TestCase
{
    public function testApplication()
    {
        $response = json('POST', '/user', ['name' => 'Sally']);

        $response
            ->assertStatus(200)
            ->assertJson([
                'created' => true
            ]);
    }
}
```

assertJson方法会将响应转换为数组并且利用PHPUnit::assertArraySubset方法来验证传入的数组是否在应用返回的JSON响应中。也就是说, 即使有其他属性
存在于该JSON响应中, 但是只要指定的片段存在, 此测试仍然会通过。

**验证完全匹配**

```
<?php
class ExampleTest extends TestCase
{
    $response = json('POST', '/user', ['name' => 'Sally']);

    $response
            ->assertStatus(200)
            ->assertExactJson([
                'created' => true,
            ]);
}
```
