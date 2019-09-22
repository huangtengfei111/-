# mybatis



```java
 @Test
    public void QueryCount() {
        String source="mybatis-config.xml";
        int count=0;
        SqlSession sqlSession=null;
 
        try {
            //1 获取mybatis-config.xml的输入流
            InputStream is = Resources.getResourceAsStream(source);
            //2 创建一个工厂，完成对配置文件的读取
            SqlSessionFactory sqlSessionFactory=new SqlSessionFactoryBuilder().build(is);
            //3 创建sqlSession，开启工厂
            sqlSession=sqlSessionFactory.openSession();
            //4 根据放入工厂的sql语句执行不同的方法
            count=sqlSession.selectOne("com.bdqn.dao.TUserMapper.queryCount");
```



### 缓存

​	MyBatis一级缓存，一级缓存的作用域scope是SqlSession。SqlSession.close()后，该缓存就不存在了

默认是开启的，当在同一个sqlsession中，查询同一结果，第一次从数据库中查询，第二次在缓存中取

一旦执行新增或更新或删除操作，缓存就会被清除，SqlSession调用了clearCache()，也会清除

查询数据的话，先从二级缓存中拿数据，如果没有的话，去一级缓存中拿，一级缓存也没有的话再查询数据库。

二级缓存是**Mapper级别**的缓存，多个SqlSession去操作同一个Mapper的sql语句，多个SqlSession可以共用二级缓存，二级缓存是跨SqlSession的。只有sqlsession进行commit后才会将数据存入到二级缓存中

二级缓存是建立在同一个namespace下的，如果对表的操作查询可能有多个namespace，那么得到的数据就是错误的。数据是过时数据。

举个简单的例子:

订单和订单详情，orderMapper、orderDetailMapper。在查询订单详情时我们需要把订单信息也查询出来，那么这个订单详情的信息被二级缓存在orderDetailMapper的namespace中，这个时候有人要修改订单的基本信息，那就是在orderMapper的namespace下修改，他是不会影响到orderDetailMapper的缓存的，那么你再次查找订单详情时，拿到的是缓存的数据，这个数据其实已经是过时的。

​	

