# How to Create custom Repository Classes

## 前提：

在之前的章节，你已经在控制器中构建并且使用更加复杂的查询。

## 目的：

为了隔离，重用和测试这些查询，为你的实体创建一个自定义的 `Repository Class` 是一个好的实践。  
包含查询逻辑的方法可以保存在这个类中

## 怎样做

1. 添加 `repository class` 名儿到你的实体映射信息中
```
namespace AppBundle\Entity;

use Doctrine\ORM\Mapping as ORM

/**
*
* @ORM\Entity(repositoryClass="AppBundle\Repository\ProductRepository")
*/
class Product{
	//
}
```
Doctrine可以在你的应用程序中自动的生成一个空的Repository Class通过之前用过的一条命令，用来生成缺少的getter和setter的命令

>php app/console doctrine:generate:entities AppBundle

2. 继承 `Doctrine\ORM\EntityRepository`  

**练习：**
添加一个方法 `findAllOrderByName()` 到新生成的ProductRepository中，这个方法将查询所有的 `Product` 实体，并且按照名字的字母顺序排列  
```
<?php 

namespace AppBundle\Repository;

use Doctrine\ORM\EntityRepository;

class Product extends EntityRepository{
	
	public function findAllOrderByName(){
		return $this->getEntityManager()
			->createQuery(
			'SELECT p FROM AppBundle:Product ORDER BY p.name ASC'
		)
		->getResult();
	}
}
```

在repository中可以通过$this->getEntityManager()获取到实体管理器。

3. 使用  

接下来你就可以使用你定义的新方法了，使用起来就像使用repository中的finder方法一样。
```
$em = $this->getDoctrine()->getManager();
$product = $em->getRepository('AppBundle:Product')
	->findAllOrderByName();
```

当使用了自定义的Repository类，你依旧可以使用finder方法，例如：`find()` 和 `findAll()`。
