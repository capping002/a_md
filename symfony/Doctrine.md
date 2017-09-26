# Doctrine

-- How to use Doctrine DBAL (如果你偏爱使用原生的数据库查询)  
-- DoctrineMongoDBBundle (你可以持久化数据到mongodb通过使用Doctrine ODM类库)  
-- How to Set external parameters in the Service Container  
-- Doctrine Field Types Reference  
-- Doctrine's Mapping Documentation  
-- Doctrine's Creating Classes for the Database and Property Mapping Documentation (如果你有自由选择你的数据库模式)  
-- migrations (迁移)  
-- Service Container  
-- Transactions and Concurrency(事物与并发) --flush()失败时考虑  
-- DoctrineFixturesBundle (使用编程的方式加载测试数据)  
-- FrameworkExtraBundle (@ParamCoverter shortcut)  
-- Querying for Objects  
-- relations  
-- Doctrine Query Language  
-- Query Builder  
-- How to Create Custom Repository Classes (已完成)  
-- Doctrine's config reference  
-- Doctrine's Mapping Types documentation  
<<<<<<< HEAD
-- How to Work with Doctrine Associations/Relations  
=======
-- How to Work with Doctrine Associations/Relations

>>>>>>> e1db72b... Doctrine完结


对于任何的应用程序，最普遍也最有挑战的尝试包括持久化数据到数据库和从数据库读取数据。
虽然symfony框架没有提供任何的组件去操作数据库，但是它紧密的结合了doctrine这个第三个的类库。
doctrine的唯一目标是提供给你强大的工具去使得数据库交互容易和灵活

## 可以学到什么
怎么样在你的symfony框架中开始杠杆效应的Doctrine，提供给你丰富的数据库交互。
这章的内容全部都是Doctrine ORM，目标是让你映射对象到一个关系型数据库

**注意**
Doctrine和Symfony是完全解耦的，在symfony中使用doctrine是可选的

## Section1
在这节中，你将配置你的数据库，创建一个product类，持久化它到数据库and取出来。

1. 配置你的数据库连接信息, 按照惯例：app/config/parameters.yml  
	通过parameter.yml定义配置项仅仅是一个惯例，当配置Doctrine，定义在那个文件的参数被映射到主配置文件中。

	优点：
	通过分离这个数据库信息到一个分离的文件，你可以容易的在每个服务器上保存不同版本的文件。  
	你也可以容易的在你的项目外存储数据库的配置信息（或者任何的敏感信息），例如在你的Apache配置中。

2. > php app/console doctrine:database:create		//创建数据库  
   > php app/console doctrine:database:drop --force	//删除数据库


###################################################	

甚至经验丰富的开发者在开始一个Symfony项目也会犯得错误：忘记在数据库中设置默认的字符集和排序规则
一种解决方案是配置服务器级别的默认值

① 为MySQL设置默认为utf8格式就是简单的增加几行到你的配置文件中（通常是my.cnf）
```
[mysqld]
# Version 5.5.3 introduced "utf8mb4", which is recommended
collation-server     = utf8mb4_general_ci # Replaces utf8_general_ci
character-set-server = utf8mb4            # Replaces utf8
```
② 你也可以为doctrine改变默认值，因此可以使用正确的字符集生产SQL
```
# app/config/config.yml
doctrine:
    dbal:
        charset: utf8mb4
        default_table_options:
            charset: utf8mb4
            collate: utf8mb4_unicode_ci
```

我们推荐摒弃MySQL的utf-8字符集设置，因为它不支持4位的Unicode编码，并且包含它们的字符将被截断

######################################################

3. 创建一个实体类 -- 在你的APPBundle下的Entity文件下

这个类文件 -- 经常叫做一个实体，意思是一个携带数据的基础类 -- 是简单的并且足够有用的，可以满足你的应用程序的需求。
这个类还不能持久化到数据库 -- 它仅仅就是一个普通的类

一旦你学会了Doctrine背后的概念，你就可以使用Doctrine为你创建简单的实体类。下面这条命令将使用交互的方式帮你建立任何的实体

> php app/console doctrine:generate:entity

4. 添加映射信息

Doctrine允许你和数据库的工作，(相对于取标量数据行到一个数组中，)使用一种更有趣的方式。在doctrine中，允许你从数据库取整个对象，也允许你持久化整个对象到数据库。
为了让doctrine可以做这些，你必须你的数据表到一个明确的PHP类，数据表中的列必须映射到对应PHP类中的明确的属性

实现：使用metadata -- 一组规则的集合，告诉Doctrine究竟这个product类和他的属性应该怎样被映射到一个具体的数据库中。
metadata可以被定义使用不同的格式，包括YMAL，XML，或者直接在类里使用DocBlock annotations

**一个bundle仅能接受一种matadata定义格式**
**数据表的名字是可选的，如果省略，则自动以实体名命名**

Doctrine允许你从种类繁多的字段类型中选择 -- see the Doctrine Field Type Reference section.

**当使用的类库或者程序也在使用annotation，你应该放置在类上 `@IgnoreAnnotation` annotation 去表明哪个annotation Symfony
应该不理睬**

验证映射信息的命令,在创建了你的实体后使用

> php app/console doctrine:schema:validate

5. 生成getter和setter

虽然Doctrine现在知道怎样持久化数据到数据库，但是这个类的本身是没什么用的。因为这个类仅仅是一个包含私有属性的规则PHP类，你需要
生成public的getter和setter方法。这样在你的程序的其他地方就可以访问了。

```
> php app/console doctrine:generate:entities AppBundle/Entity/Product
# 生成APPBundle下的所有entities
> php app/console doctrine:generate:entities AppBundle
# 生成Acme命名空间下的所有entities
> php app/console doctrine:generate:entities Acme
```

这个命令是安全的，你可以一次又一次的运行。它只会生成不存的getter和setter方法（它不会替代你存在的方法）。

**记住这条生成getter和setter的command。如果有必要的话，你应该查看生成的getter和setter方法并添加自己的逻辑去适应你的应用程序**

More about doctrine:generate:entities
  a) 在你的实体类中生成getter和setter方法
  b) 在实体配置项中加入 `@ORM\Entity(repository='...')` annotation 会生成repository类
  c) 生成 1:n 和 n:m 关系的适当构造器

这条命令会保存一个 `Product.php` 的备份文件，叫做 `Product.php~` 。在一些情况下，这个文件的存在会造成一个 'Cannot redeclare class'
的错误。移除他是安全的，你也可以使用 `--no-backup` 选项去阻止生成这些备份文件

**你也可以手写getter和setter**

6. Creating the Database Tables/Schema

你现在有一个带有映射信息的可用的 `Product` 类，因此Doctrine知道究竟怎样去持久化他。当然，你不需要在你的数据库中有相应的数据表。
幸运的是，Doctrine可以为你的应用程序中每个可知实体创建数据表。run:

> php app/console doctrine:schema:update --force

事实上，这条语句令人难以置信的强大。它比较你的数据库应该是怎样的（基于你的实体的映射信息）和事实上怎样的，然后执行SQL语句更新
需要更新的数据库模式。换句话说，如果你增加了一个新的属性和映射信息在 `Product` 表中，然后运行这条命令，它将执行"Alter table"语
句增加新的列到存在的 `Product` 表中。

一个更好的方式去利用这个功能是通过迁移，允许你生成SQL语句并且存储他们到迁移类中，这个类可以在生产服务器上系统运行，以便安全可靠
地更新和跟踪数据库的架构的更改。

无论你是否利用这个迁移，这个 `doctrine:schema:update` 命令仅仅在开发模式下被使用。在生产环境不应该使用。

你的数据库现在有全功能的 `Product` 表，字段匹配着确定的matadata.

7. 持久化对象到数据库

现在你已经映射 `Product` 实体到相应的 `Product` 表，你准备去持久化 `Product` 对象到数据库。在控制器中，这是特别容易的。在Bundle
下的 DefaultController 下增加下列的方法。
```
use AppBundle\Entity\Product;
use Symfony\Component\HttpFoundation\Response;

public function createAction(){
	$product = new Product();
	$product->setName('Keyboard');
	$product->setPrice(19.99);
	$product->setDescription('Ergonomic and stylish');

	$em = $this->getDoctrine()->getManager();
	$em->persist($product);
	$em->flush();

	return new Response('Saved new product with id ' . $product->getId());
}
```
文章展示了在控制器中使用Doctrine的getDoctrine()方法。这个方法是得到Doctrine服务的快捷方式。你可以通过注入的方式在任何地方
使用Doctrine。看 `Service Container` 部分获取更多信息，当创建自己的服务时会用到。

解释：取到 `$product` 对象，并为其设置值。获取到Doctrine的实体管理对象，这个对象主要负责持久化对象到数据库和从数据库取对象。然后
使用 `persist($product)` 去告诉Doctrine去管理 `$product` 对象。执行了 `flush` 命令，Doctrine会浏览它管理的所有对象，判断它们
是否需要持久化到数据库

In fact, 因为Doctrine知道所有的你管理的实体，当你调用 `flush()` 方法时，它计算所有的改变，然后按顺序执行语句。它利用缓存准备语句
稍稍提高性能。例如，如果你实例化100个 `Product` 对象，然后调用 `flush()`，Doctrine将使用一个准备好的语句对象执行100条 `INSERT` 语句。

如果 `flush()` 调用失败，将抛出一个 `Doctrine\ORM\ORMException` 异常。see--(Transactions and Concurrency)

**无论是创建对象还是更新对象，工作流程是相同的。在接下来的章节，你会看到Doctrine足够的聪明：如果这个实体已经存在于实体中，Doctrine就会
自动去分发一个 `UPDATE` 操作**

Doctrine提供了一个类库，允许你以编程的方式将测试数据加载到项目中(i.e."fixture data")。-- (DoctrineFixturesBundle)

8. Fetching Objects From the Database

从数据库取出一个对象更容易。假设你配置了一个路由去展示一个 `special Product` -- 基于它的id值。
```
public function showAction()
{
	$product = $this->getDoctrine()
		->getRepository('AppBundle:Product')
		->find($productId);

}
```
使用 `@ParamConverter shortcut` 可以不写一行代码就实现相同的效果。详情请看(FrameworkExtraBundle documentation)

当你查询一个特定的对象的时候，你总是使用它的"repository"。你可以把一个repository想象为一个PHP的类，它仅仅帮组你取某个确定类中的实体。  
以下方式你可以通过一个repository对象得到一个实体类
```
$repository = $this->getDoctrine()
	->getRepository('AppBundle:Product');
```

`AppBundle:Product`是(`AppBundle\Entity\Product`)的快捷方式，在Doctrine中你可以在任何地方使用这个快捷方式去代替实体的完整类名，只要
你的类名在你的Bundle下的Entity命名空间下，他就能起作用。

一旦你得到了repository对象，你就可以使用下列所有有用的方法
```
$repository = $this->getDoctrine()->getRepository('AppBundle:Product');
<!-- 通过主键查询单个商品 -->
$product = $repository->find($productId);

<!-- 动态的方法名基于一个字段值去查询单个产品 -->
$product = $repository->findOneById($productId);
$product = $repository->findONeByName('Keyboard');

<!-- 动态的方法名基于一个字段值查询一组产品 -->
$product = $repository->findByPrice(19.99);

<!-- 查找所有的产品 -->
$product = $repository->findAll();
```
当然了，你也可以发出混合查询，在(Querying for Objects)章节你可以学到更多

你也可以利用 `findBy()` 和 `findOneBy()` 这两个有用的方法在混合条件下容易的取出对象。
```
$repository = $this->getDoctrine()->getRepository('AppBundle:Product');

<!-- 匹配name和price，查询单个产品 -->
$product = $repository->findOneBy(
	array('name'=>'Keyboard', 'price'=>19.99)
);

<!-- 匹配name,根据price排序，查找多个产品 -->
$product = $repository->findBy(
	array('name'=>'Keyboard'),
	array('price'=>'ASC')
);
```
当渲染一个页面请求做一些数据库的交互，这个网页下面的调试工具会展示SQL语句的数量和执行它们所花费的时间

如果database queries数量太多，这个图标就会变黄去证明可能有一些错误发生。单击图标打开Symfony Profile，并查看执行的确切查询。

9. Updating an Object

一旦你从Doctrine中取了一个对象，更新它是容易的。假如你有一个路由将产品的ID映射到控制器中的更新操作上：
```
public function updateAction($productId){
	$em = $this->getDoctrine()->getManager();
	$product = $em->getRepository('AppBundle:Product')->find($productId);

	if(!$product){
		throw $this->createNotFoundException(
			'No product found for id ' . $productId
		);
	}

	$product->setName('New Product name!');
	$em->flush();

	return $this->redirectToRoute('homepage');
}
```
更新一个对象仅仅包涵三步：
 1. 从Doctrine中取对象；
 2. 修改这个对象；
 3. 在实体管理器上调用flush();

**注意：**调用 `$em->persist($product)` 不是必须的。调用这个方法就是告诉Doctrine取管理或者说监听这个 `$product` 对象。
在这种情况下，因为你从Doctrine中取 `$product` 对象，所以它已经被监听了。

10. Deleting an Object

删除一个对象是非常相似的，但是需要去调用实体管理对象[entity manager]的 `remove()` 对象。
```
$em->remove();
$em->flush();
```

11. Querying for Objects

你已经看到repository对象允许你运行基本的查询而无需任何工作：
```
$repository = $this->getDoctrine()->getRepository();

$product = $repository->find($productId);
$product = $repository->findOneByName('Keyboard');
```

当然了，Doctrine允许你去写更加复杂的查询使用Doctrine Query Language(DQL)。DQL和SQL是相似的除了你应该想象你查询的是一个实体类的
一个或者多个对象，而不是查询记录。  
当你使用Doctrine查询时，你有两个选择：写纯的DQL查询或者使用Doctrine的Query Builder。

12. Querying for Objects with DQL

查询花费大于19.99，从便宜到贵排序。你可以使用DQL，Doctrine的原生的类SQL的语言，为这个情景去构造一个查询：
```
$em = $this->getDoctrine()->getManager();
$query = $em->createQuery(
	'SELECT p
	FROM AppBundle:Product
	WHERE p.price > :price
	ORDER BY p.price ASC'
)->setParamter('price', 19.99);

$product = $query->getResult();
```

如果你使用SQL是舒服的，那么DQL应该感觉也非常自然。最大的不同是你应该考虑查询的是对象，而不是数据库中的记录。因此你select from
the AppBundle:Product 实体，然后起别名为 p 。

记住 `setParamter()` 方法。当使用Doctrine时，设置一个外部的值做"占位符有"总是一个好主意，这样可以防止SQL注入攻击。

`getResult()` 方法返回结果的一个数组，得到仅仅一个值，你可以使用 `getOneOrNullResult()`：
```
$product = $query->setMaxResult(1)->getOneOrNullResult();
```

DQL语法令人难以置信的强大，允许你easily join between entities(关系主题将在后面讨论), group, etc.更多信息，请看官方的文档(Doctrine Query 
Language)

13. Querying for Objects Using Doctrine's Query Builder

替代写一个DQL字符串，你可以使用一个叫做 `QueryBuilder` 的有用对象为你构建字符串。这是非常有用的在基于动态情景下的实际查询中，因为当你
连接字符串时，你使用DQL的代码很快就变得难读起来。
```
$repository = $this->getDoctrine()
	->getRepository('AppBundle:Product');

//createQueryBuilder()自动从AppBundle:Product选择
//并且起别名为p
$query = $repository->createQueryBuilder('p')
	->where('p.price > :price')
	->setParameter('price', '19.99')
	->orderBy('p.price', 'ASC')
	->getQuery();

$products = $query->getResult();
//只得到一条结果：
// $prouduct = $query->setMaxResult(1)->getOneOrNullResult();
```

`QueryBuilder` 包含构建查询的所有方法。通过调用 `getQuery()` 方法，query builder返回一个普通的 `query` 对象。这个对象
可以用来获取到查询的结果。

了解更多关于Doctrine的QueryBuilder信息，请教Doctrine的 (Query Builder) 文档。

14. 在 Repository Classes 中组织自定义的Queries

所有之前的章节的查询都被直接写在控制器中。但是为了组织，Doctrine提供了特殊的 repository classes 允许你写所有的查询逻辑在一个中心位置。

see How to Create Custom Repository Classes for info.

15. Configuration

Doctrine是高度可配置的，虽然你可能不需要去关系它的大量选项。去寻找更多关于配置Doctrine的信息，看Doctrine的config reference章节。

16. Doctrine的字段映射参考

Doctrine与生俱来就有众多的字段类型。其中的每一个将PHP数据类型映射到你正在使用的任何数据库中的特定列类型。对于每个字段类型，这个
`Column` 可以进一步配置， 设置 `length`, `nullable` 行为，`name` 和另外的属性。了解一系列可获得的类型和更多信息，see Doctrine's
Mapping Types documentation.

17. Relationship and Associations

Doctrine 提供了管理数据库的关系所需的所有功能(also known as associations)。For info，see How to Work with Doctrine Associations/Relations

18. 最后的思考

使用Doctrine，您可以专注于您的对象及其在应用程序中的使用方式，并担心数据库持久性。 这是因为Doctrine允许您使用任何PHP对象
来保存数据，并依赖映射元数据信息将对象的数据映射到特定的数据库表。

Doctrine有很多复杂的特点需要学习，例如，关系，复杂查询和事件监听。




#######################################
## Doctrine的操作总结  
-- getDoctrine() : 得到Doctrine的快捷方式  
-- 
#######################################
