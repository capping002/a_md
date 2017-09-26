# microtime

micrototime -- 返回当前 Unix 时间戳和微秒数

## 说明

> mixed microtime([ bool $get_as_float ])

**mixed:说明一个参数可以接受多种(但不一定是所有的)不同的类型。

**microtime()当前 Unix 时间戳以及微妙数。本函数仅在支持 gettimeofday() 系统调用的操作系统下可用。

如果调用时不带可选参数, 本函数以 "msec sec" 的格式返回一个字符串, 其中 sec 是自 Unix 纪元( 0:00:00 January 1, 1970 GMT )起到现在的秒数, 
msec 是微秒部分。字符串的两部分都是以秒为单位返回的。

如果给出了 get_as_float 参数并且其值等价于 TRUE, microtime() 将返回一个浮点数。

> Note: get_as_float参数是 PHP5.0.0 新加的。

**Example #1 microtime() 对脚本的运行计时。
```
<?php 
/**
 * Simple function to replicate PHP 5 behaviour
 */
function microtime_float(){
	list($usec, $sec) = explode(" ", microtime());
	return ((float)$usec + (float)$sec);
}
$time_start = microtime_float();

// Sleep for a while
usleep(100);

$time_end = micrototime_float();
$time = $time_end - $time_start;

echo "Did nothing in $time seconds\n";
 ?>
}
```
测试jenkins
