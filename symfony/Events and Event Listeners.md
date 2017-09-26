# Events and Event Listeners

-- KernelEvents class  
-- custom events  
-- getResponseForExceptionEvent  
-- KernelEvents  
-- The EventDispatch Component  
-- How to Embed Controller in a Template (如何在一个模板中嵌入控制器)

一个Symfony程序的执行，大量的事件通知被触发。你的程序可以监听这些通知，并且通过执行任何代码响应他们。

Symfony提供的内部事件被定义在 `KernelEvents` 类中，第三方的 `Bundles` 和类库也可以触发大量的事件，并且你
自己的应用可以触发 custom events。

在这篇文章中显示的所有例子都使用 `KernelEvents::EXCEPTION` 为了一致性的目的。在你自己的应用程序中，你可以
使用任何的事件，甚至混合几个在同一个 Subscriber 中。

## Creating an Event Listener  

监听一个事件最普遍的方式是注册一个 **event listener**:
```
// src/AppBundle/EventListener/ExceptionListener.php
namespace AppBundle\EventListener;

use Symfony\Component\HttpKernel\Event\GetResponseForExceptionEvent;
use Symfony\Component\HttpFoundation\Reponse;
use Symfony\Component\HttpKernel\Exception\HttpExceptionInterface;

class ExceptionListener
{
	public function onKernelException(GetResponseForExceptionEvent $event)
	{
		// 你从接收到的事件get到异常对象
		$exception = $event->getException();
		$message = sprintf(
			'My Error says: %s with code: %s',
			$exception->getMessage(),
			$exception->getCode()
		);

		// 自定义你的响应对象去展示异常详情
		$response = new Response();
		$response->setContent($message);

		// HttpExceptionInterface是一个特殊类型的异常
		// 携带着 status code 和 header detail
		if ($exception instanceof HttpExceptionInterface) {
			$response->setStatusCode($exception->getStatusCode);
			$response->headers->replace($exception->getHeaders());
		} else {
			$response->setStatusCode(Response::HTTP_INTERNAL_SERVER_ERROR);
		}

		// 将修改后的响应对象发送到事件
		$event->setReponse($response);
	}
}
```
每个事件接受略有不同的 `$event` 对象。对于 `kernel.exception` 事件， it is `GetResponseForExceptionEvent`。去了解每个事件监听接收什么
类型的对象，看 `KernelEvents` 或者 看关于你正在监听的这个明确的事件的文档。

现在这个类被创建, 你只需将这个类注册为一个服务并且使用一个特殊的 "tag" 通知 Symfony `kernel.exception` 是一个 "listeners":

```
# app/config/services.yml
services:
	app.exception_listener:
		class: AppBundle\EventListener\ExceptionListener
		tags:
			- { name: kernel.event_listener, event: kernel.exception }
```

有一个可选的 tag 属性叫 `method`, 用于定义事件被触发时要执行的方法。默认情况下, 该方法的名称为 on +　"camel-cased event name"。如果事件
是 `kernel.exception`, 默认情况下执行的方法是 `onKernelException()`。

另一个可选的 tag 属性叫 `priority`, 默认是0, 它控制着监听器的执行顺序(优先级越高, listener越早被执行)。当你需要保证一个 listeners 早于
另一个执行, 这是有用的。Symfony 内部的 listener 的优先级通常使用 -255 到 255 的范围, 但是你自己的 listener 任何的正整数或负整数。

## Creating an Event Subscriber

另外一种监听事件的方式是通过 *event subscriber*, event subscriber是一个定义一个或者多个方法的类, 用来监听一个或者多个事件。event 
subscribers 和 event listeners 的主要的不同是 subscribers 总是知道他们正在监听哪个事件。
	
在给定的订阅者中(在给定的PHP类中), 不同的方法可以监听相同的事件。执行方法的顺序被每个方法的 `priority` 参数定义(越高的优先级越早
被调用)。去了解更多关于 event subscriber, 读 The EventDispatch Component。
	
下面的例子展示的是一个定义了几个方法的 event subscriber, 这几个方法都是监听 `kernel.exception` 事件:

```
// src/AppBundle/EventSubscriber/ExceptionSubscriber.php
namespace AppBundle\EventSbuscriber;

use Symfony\Component\EventDispatcher\EventSubscriberInterface;
use Symfony\Component\HttpKernel\Event\GetResponseForExceptionEvent;
use Symfony\Component\HttpKernel\KernelEvents;

class ExceptionSubscriber implements EventSubscriberInterface
{
	public static function getSubscribedEvents(){
		// return the subscriber events, their methods and priorities
		return array(
			KernelEvents::EXCEPTION => array(
				array('processException', 10),
				array('logException', 0),
				array('notifyException', -10)
			)
		);
	}

	public function processException(GetResponseForException $event)
	{
		// ...
	}

	public function loginExcption(GetResponseForException $event)
	{
		// ...
	}

	public function notifyException(GetResponseForException $event){
		// ...
	}
}
```

Now, 你只需要注册这个类为一个服务并且增加 `kernel.event_subscriber` 标签告诉 symfony 这是一个 event subscriber。

```
YAML
# app/config/services.yml
services:
	app.exception_subscriber:
		class: AppBundle\EventSubscriber\ExceptionSubscriber
		tags: 
			- { name: kernel.event_subscriber }
```

## Request Events, Checking Types

每个页面可以进行多个请求(一个主请求，多个子请求 -- 通常通过 How to Embed Controllers in a Template)。对于核心的 Symfony 事件, 
你应该检查这个事件是否是一个主请求或者是一个子请求。
```
// src/AppBundle/EventListener/RequestListener.php
namespace AppBundle\EventListener;

use Symfony\Component\HttpKernel\Event\GetResponseEvent;
use Symfony\Component\HttpKernel\HttpKernel;
use Symfony\Component\Httpkernel\HttpKernelInterface;

class RequestListener
{
	public function onKernelRequest(GetResponseEvent $event)
	{
		if (!$event->isMasterRequest()) {
			// don't do anything if it's not the master request
			return;
		}

		// ...
	}
}
```

确定的事情, 像在真实的请求上检查信息, 可能不需要在子请求监听器上完成。

## Listeners or Subscribers

Listeners 和 Subscribers 被用在相同的应用程序中(不明确的), 决定使用哪个通常是由个人品味决定的。然而，他们都有一些小的优势:

- **Subscriber更容易去重用**因为事件的知识保存在类中而不是在服务中定义。这就是 Symfony 内部使用 subscribers 的原因。
- **Listeners更加灵活**因为 bundles 可以根据一些配置值有条件的启用或者禁用他们。

## Debugging Event Listeners(调试事件监听器)

你可以通过控制台打印所有在事件调度程序中注册的监听器。要显示所有的事件和他们的听众, 运行:
> php app/console debug:event-dispatcher

你可以通过指定其名字获取特定事件的注册监听器
> php app/console debug:event-dispatcher kernel.exception
	