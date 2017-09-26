# QueryBuilder

## Working with QueryBuilder

1. 高级API方法

为了简化在Doctrine中构建查询的方式，我们可以利用helper方法。对于所有的基础代码，有一系列有用的方法去简化程序员的生活。
为了说明他们是怎样工作的，这儿有使用 QueryBuilder helper 方法重写的例子6。
```
<?php
// $qb instanceof QueryBuilder
// $qb = $this->getEntityManager()->createQUeryBuilder();

$qb->select('u')
	->from('User', 'u')
	->where('u.id = ?1')
	->orderBy('u.name', 'ASC');
```

QueryBuilder 帮助器方法被认为是构造DQL查询的标准方法。虽然它是支持的，但是尽量避免使用基于字符串的查询，并且大大鼓励使用
 `$qb->expr()->*` 方法。这是一个转换的例子8建议的标准方式构建查询：

```
<?php
// $qb instanceof QueryBuilder

$qb->select(array('u'))
	->from('User', 'u')
	->where($qb->expr()->orX(
		$qb->expr()->eq('u.id', '?1')
		$qb->expr()->like('u.nickname', '?2')
	))
	->orderBy('u.username', 'ASC');
```
以下是QueryBulider中所有可用的帮助方法的完整列表：
```
<?php
class QueryBuilder{
    // Example - $qb->select('u');
    // Example - $qb->select(array('u', 'p'));
    // Example - $qb->select($qb->expr()->select('u', 'p'));
    public function select($select=null);
    
    // addSelect不会覆盖之前的调用的select
    // Example - $qb->select('u')
    //              ->addSelect('p.area_code');
    public function addSelect($select=null);
    
    // Example - $qb->delete('User', 'u')
    public function delete($delete=null, $alias=null);
    
    // Example - $qb->update('Group', 'g')
    public function update($update=null, $alias=null);
    
    // Example - $qb->set('u.firstName', $qb->expr()->literal('Arnold'))
    // Example - $qb->set('u.numChilds', 'u.numChilds+?1')
    // Example - $qb->set('u.numChilds', $qb->expr()->sum('u.numChilds', '?1'))
    public function set($key, $value)
    
    // Example - $qb->from('Phonenumber', 'p')
    // Example - $qb->from('Phonenumber', 'p', 'p.id')
    public function from($from, $alias, $index=null);
    
    // Example - $qb->join('u.Group', 'g', Expr\Join::WITH, $qb->expr()->eq('u.status_id', '?1'))
    // Example - $qb->join('u.Group', 'g', 'WITH', 'u.status = ?1')
    // Example - $qb->join('u.Group', 'g', 'WITH', 'u.status = ?1', 'g.id')
    public function join($join, $alias, $conditionType=null, $condition=null, $indexBy=null);
    
    // Example - $qb->innerJoin('u.Group', 'g', Expr\Join::WITH, $qb->expr()->eq('u.status_id', '?1'))
    // Example - $qb->innerJoin('u.Group', 'g', 'WITH', 'u.status = ?1')
    // Example - $qb->innerJoin('u.Group', 'g', 'WITH', 'u.status = ?1', 'g.id')
    public function innerJoin($join, $alias, $conditionType=null, $condition=null, $indexBy=null);
    
    // Example - $qb->leftJoin('u.Phonenumbers', 'p', Expr\Join::WITH, $qb->expr()->eq('u.status_id', '?1'))
    // Example - $qb->leftJoin('u.Phonenumbers', 'p', 'WITH', 'p.area_code=55')
    // Example - $qb->leftJoin('u.Phonenumbers', 'p', 'WITH', 'p.area_code=55', 'p.id')
    public function leftJoin($join, $alias, $conditionType=null, $condition=null, $indexBy=null)
    
    // 注意：->where()会覆盖之前设置的所有条件
    //
    // Example $qb->where('u.firstName = ?1', $qb->expr()->eq('u.surname', '?2'))
    // Example $qb->where($qb->expr()->andX($qb->expr()->eq('u.firstName', '?1'), $qb->expr()->eq('u.surname', '?2')))
    // Example $qb->where('u.firstName = ?1 AND u.surname = ?2')
    public function where($where);
    
    // 注意：->andWhere()可以直接使用，而不需要前面有->where()
    // Example - $qb->andWhere($qb->expr()->orX($qb->expr()->lte('u.age', 40), 'u.numChild = 0'))
    public function andWhere($where);
    
    // Example - $qb->orWhere($qb->expr()->between('u.id', 1, 10))
    public function orWhere($where);
    
    // 注意：groupBy()覆盖之前设置的groupBy条件
    // Example - $qb->groupBy('u.id')
    public function groupBy($groupBy);
    
    // Example - $qb->addGroupBy('g.name')
    public function addGroupBy($groupBy);
    
    // 注意：having覆盖之前设置的所有having条件
    // Example - $qb->having('u.salary >= ?1')
    // Example - $qb->having($qb->expr()->gte('u.salary', '?1'))
    public function having($having);
    
    // Example - $qb->addHaving($qb->expr()->gt($qb->expr()->count('u.numChild'),0))
    public function addHaving($having);
    
    // Example - $qb->orHaving($qb->expr()->lte('g.managerLervel', '100'))
    public function orHaving($having);
    
    // 注意：->orderBy()覆盖之前设置的orderBy条件
    // Example - $qb->orderBy('u.surname', 'DESC')
    public function orderBy($sort, $order=null);
    
    // Example - $qb->addOrderBy('u.firstName')
    public function addOrderBy($sort, $order=null); //Defaule $order = 'ASC'
}
```

2. 绑定参数到你的query

Doctrine支持动态绑定参数到查询中，像准备查询一样。你可以使用数字或者字符串作为占位符，虽然它们的语法略有不同。另外，你必须做出你的选择，因为
混合这两种语法是不允许的。下面是一个绑定参数的一个简单例子：

```
<?php
// $qb instanceof QueryBuilder

$qb->select('u')
    ->from('User', 'u')
    ->where('u.id = ?1')
    ->orderBy('u.name', 'DESC')
    ->setParameter(1, 100);
```

你不需要强制枚举你的占位符，因为替代语法存在。

```
<?php
// $qb instanceof QueryBuilder
$qb->select('u')
    ->from('User', 'u')
    ->where('u.id = :identifier')
    ->orderBy('u.name', 'ASC')
    ->setParameter('identifier', 100);
```
注意：数值占位符开始是?然后跟一个数字，命名占位符开始是:然后后面跟一个字符。


