# Logging with Monolog

-- Monolog  
-- send an email when an error occurs  
-- LoggerInterface  

Symfony 生来带有一个外部的类, 叫做 Monolog。 允许你创建可以保存在不同地方的日志。

## Logging a Message

记录一个信息, 在控制器中从 container 中取 `logger` 服务。
```
use Psr\Log\LoggerInterface;

public function indexAction(LoggerInterface $logger)
{
	// alternative way of getting the logger
	// $logger = $this->get('logger');

	$logger->info('I just got the logger');
	$logger->error('An error occurred');

	$logger->critical('I left the oven on!', array(
		// include extra "context" info in your logs
		'cause'		=>	'in_hurry'
	));

	// ...
}
```

`logger` services 对不同的级别/不同优先级的日志有不同的方法。你可以配置日志基于不同的级别的信息做不同的事(e.g. send an email when an error occurs).

有关 logger 上的所有方法列表, 请参见 LoggerInterface。

## Where Logs are Stored

