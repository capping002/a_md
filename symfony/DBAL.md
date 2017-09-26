# DBAL

-- Database and Doctrine ORM (已完成)  
-- Doctrine DBAL Documentation  
-- Doctrine DBAL Configuration  
-- Doctrine's Custom Mapping Types

这篇文章是关于Doctrine的DBAL的。通常情况下，你使用的更高层次的 Doctrine ORM， 是通过DABAL在幕后实现数据库的连接与交互的。
想要了解更多的 Doctrine ORM ,see "Database and Doctrine ORM"。

这 Doctrine Database Abstraction Layer (DBAL) 是一个基于PDO的抽象层，并且提供了一组直观和灵活的API去和目前最流行的关系型数据库交互。
换句话说DBAL类库使得查询和其他数据库操作变得容易。

阅读官方的 Doctrine DBAL Documentation 去学习所有的细节和 Doctrine DBAL 类库的功能
 
1. Configure the database connection parameters:

完整的DBAL配置项，或者学习怎么配置多连接， see Doctrine DBAL Configuration.  
通过 `database_connection` 服务连接， 你可以访问Doctrine DBAL。
```
class UserController extends Controller
{
	public function indexAction()
	{
		$conn = $this->get('database_connection');
		$user = $conn->fetchAll('SELECT * FROM users');

		// ...
	}
}
```
2. 注册自定义映射类型

你可以通过Symfony的配置文件注册自定义的映射类型。它们将被添加到所有配置的连接。更多关于自定义的映射类型，阅读它们文档中的
 Doctrine's Custom Mapping Types 章节。

```
# app/config/config.yml
doctrine:
	dbal:
		types:
			custom_first: AppBundle\Type\CustomFirst
			custom_second: AppBundle\Type\CustomSecond
```

3. 在SchemaTool中注册自定义映射类型

SchemaTool被用来检查数据库去比较这个模式。去实现这个尝试,