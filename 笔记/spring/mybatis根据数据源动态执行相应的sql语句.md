# mybatis根据数据源动态执行相应的sql语句

## 方法一

### MyBatisConfigurer 配置

SqlSessionFactory 配置里面设置 DatabaseIdProvider 

```java
@Configuration
public class MybatisConfigurer {

    private DatabaseIdProvider getDataBaseIdProvider(){
        DatabaseIdProvider databaseIdProvider = new VendorDatabaseIdProvider();
        Properties properties = new Properties();
        properties.setProperty("Oracle","oracle");
        properties.setProperty("MySQL","mysql");
        databaseIdProvider.setProperties(properties);

        return databaseIdProvider;
    }

    @Bean
    public SqlSessionFactory sqlSessionFactoryBean(DataSource dataSource) throws Exception {
        SqlSessionFactoryBean factory = new SqlSessionFactoryBean();
        factory.setDataSource(dataSource);
        factory.setTypeAliasesPackage(MODEL_PACKAGE);

        factory.setDatabaseIdProvider(getDataBaseIdProvider());

        //配置分页插件，详情请查阅官方文档
        PageHelper pageHelper = new PageHelper();
        ........
```

### xml sql语句书写

```xml
<select id="SelectTime" resultType="String" databaseId="mysql">
 SELECT  NOW() FROM dual
</select>

  <select id="SelectTime" resultType="String" databaseId="oracle">
 SELECT  'oralce'||to_char(sysdate,'yyyy-mm-dd hh24:mi:ss')  FROM dual
</select>
```

根据 databaseId 来判断

## 方法二

使用自定义mybatis 配置（参考文章：https://blog.csdn.net/yali_aini/article/details/90671470）

```java
RootBeanDefinition sqlSession = new RootBeanDefinition(SqlSessionFactoryBean.class);
try {
    sqlSession.getPropertyValues()
            .add("dataSource", new RuntimeBeanReference("datasource" + i ))
            .add("vfs", SpringBootVFS.class)
            .add("mapperLocations", new PathMatchingResourcePatternResolver().getResources(mapperLocations))
        	.add("databaseIdProvider", getDataBaseIdProvider());
} catch (IOException e) {
    logger.error(sqlSessionFactoryName + " bind fail!!!");
}





    private DatabaseIdProvider getDataBaseIdProvider() {
        DatabaseIdProvider databaseIdProvider = new VendorDatabaseIdProvider();
        Properties properties = new Properties();
        properties.setProperty("Oracle", "oracle");
        properties.setProperty("MySQL", "mysql");
        databaseIdProvider.setProperties(properties);

        return databaseIdProvider;
    }

```

如果该代码不能修改，那就。如下操作，因为 BeanFactoryPostProcessor 在 BeanDefinitionRegistryPostProcessor 之后加载。

```java
@Component
public class XXX implements BeanFactoryPostProcessor {

    private DatabaseIdProvider getDataBaseIdProvider() {
        DatabaseIdProvider databaseIdProvider = new VendorDatabaseIdProvider();
        Properties properties = new Properties();
        properties.setProperty("Oracle", "oracle");
        properties.setProperty("MySQL", "mysql");
        databaseIdProvider.setProperties(properties);

        return databaseIdProvider;
    }

    @Override
    public void postProcessBeanFactory(ConfigurableListableBeanFactory configurableListableBeanFactory) throws BeansException {
        for (String beanDefinitionName : configurableListableBeanFactory.getBeanDefinitionNames()) {
            if (beanDefinitionName.startsWith("sqlSessionFactory")) {
                BeanDefinition definition = configurableListableBeanFactory.getBeanDefinition(beanDefinitionName);
                definition.getPropertyValues().add("databaseIdProvider", getDataBaseIdProvider());
            }
        }
    }
}
```

xml sql 书写还是跟上文一样