# Trait

-- 传统多继承和 Mixin 类相关典型问题

自PHP5.4.0起, PHP实现了一种代码复用的方法, 称为Trait。

Trait是为类型PHP的单继承语言而准备的一种代码复用机制。Trait 为了减少单继承语言的限制, 使开发人员可以自由地在不同层次结构独立的类中复用 method。
Trait 和 Class 组合的语义定义了减少复杂性的方式, 避免了传统多继承和 Mixin 类相关典型问题。

Trait 和 Class 相似, 但仅仅旨在用细粒度和一致性的方式来组合功能。**无法通过 trait 自身来实例化**。它为传统继承增加了水平特性的组合, 也就是说, 
应用的几个Class之间不需要继承。

**Example #1 Trait示例**
```
<?php 
trait ezcReflectionReturnInfo{
    function getReturnType(){/*1*/}
    function getReturnDescription(){/*2*/}
}

class ezcReflectionMethod extends ReflectionMethod
{
    use ezcReflectionReturnInfo;
    /* ... */
}

class ezcReflectionFunction extends ReflectionMethod
{
    use ezcReflectionReturnInfo;
    /* ... */   
}
?>
```

## 优先级


从基类继承的成员会被 trait 插入的成员所覆盖。优先顺序是来自当前类的成员覆盖了trait的方法, 而trait则覆盖了被继承的方法。  
优先级 : 当前类 > trait > 基类

**Example #2 优先顺序实例**

```
<?php  
class Base
{
    public function sayHello()
    {
        echo 'Hello ';
    }
}

trait SayWorld()
{
    public function sayHello()
    {
        parent::sayHello();
        echo 'World!';
    }
}

class MyHelloWorld extends Base
{
    use SayWorld;
}

$obj = new MyHelloWorld();
$obj->sayHello();

?>
```
以上例程会输出
> Hello World!

## 多个trait

通过逗号分割, 在 use 声明列出多个 trait, 可以都插入到一个类中。

## 冲突的解决

# 未完待续 ...
