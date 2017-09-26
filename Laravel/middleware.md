# 中间件

## 创建中间件

> php artisan make:middleware test

## 前置中间件和后置中间件

```
return $next($request);
```
```
$response = $next($request);
return $response;
```

## 注册中间件

kernel.php -> middleware -> middlewareGroup -> middlewareRoute

## 中间件参数

```
Route::get('post/{id}', function() {
	
})->middleware('role:edit');
```

## Terminable中间件

场景: HTTP响应发送到浏览器后运行处理一些任务, 
例子: Laravel内置的[session]中间件存储的session数据是在响应发送到浏览器后才进行写入的。
实现: 在中间件中定义一个`terminable`方法, 它会在响应发生后自动被调用。
```
public function terminate($request, $response) {
	
}
```
注意: 定义了就要加入全局中间件列表中。