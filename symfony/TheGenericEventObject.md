# The Generic Event Object (通用的事件对象)

-- base Event class  
-- Generic  


EventDispatch 组件提供的基础 event 类是故意稀疏的, 允许通过OOP的 inheritance 为特定事件创建API, 这让复杂程序中的代码优雅，可读。

GenericEvent 是可得 -- 为了那些希望始终只使用一个事件对象在他们的应用程序中的人。它适用于大多数的目的, 开箱即用, 因为它遵循标准的观察者模式, 其中事件对象
封装了一个事件 "主体", 但是增加了一个可选的额外参数。

GenericEvent 除了基类 Event 还有一个简单的API

- __construct() : 构造函数接受事件主体和任何参数;
- getSubject() : 获取主体;
- setArgument() : 通过键设置参数;
- setArguments() : 设置参数数组;
- getArgument() : 通过键获取参数;
- getArguments() : 获取所有的参数;
- hasArgument() : 如果参数的 key 存在的话返回 true


