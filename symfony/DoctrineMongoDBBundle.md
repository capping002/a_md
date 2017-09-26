# DoctrineMongoDBBundle

-- documentation (more about the Doctrine MongoDB ODM)

MongoDB Object Document Mapper (ODM) 与 Doctrine2 的 ORM 的工作原理是非常相似的。换句话说：像Doctrine2的ORM一样，使用Doctrine的ODM你仅仅是操作简单的
php类，然后透明的从MongoDB存取数据

## A Simple Example:A Product 

1. Create an AcmeStoreBundle

> php app/console generate:bundle --namespace=Acme/StoreBundle

