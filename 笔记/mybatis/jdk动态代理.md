# jdk 动态代理



```
public class PkuSession {

    public <T> T getMapper(Class<T> clz){
        Class [] classes = new Class[]{clz};
        Object proxyInstance = Proxy.newProxyInstance(PkuSession.class.getClassLoader(), classes, new PkuInvocationHandle());

        return (T)proxyInstance;
    }

    private PkuSession(){

    }

    public static PkuSession getPkuSession(){
        return InnerPkuSession.PKU_SESSION;
    }

    private static class InnerPkuSession{
        private static final PkuSession PKU_SESSION = new PkuSession();
    }

}
```



在 InvocationHandle 子类里面进行处理



```
package com.zzq.mybatis;

import com.zzq.mybatis.annotation.Select;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import javax.persistence.Column;
import javax.persistence.Id;
import javax.persistence.Transient;
import java.lang.reflect.*;
import java.sql.*;
import java.util.*;
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class PkuInvocationHandle implements InvocationHandler {

    private Logger LOG = LoggerFactory.getLogger(PkuInvocationHandle.class);

    private static final MybatisDatSource mybatisDatSource = new MybatisDatSource();

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {


        if (method.isAnnotationPresent(Select.class)) {
            String sql = method.getAnnotation(Select.class).value();

            Pattern compile = Pattern.compile("\\{.*?}");
            Matcher matcher = compile.matcher(sql);

            if (matcher.find()) {
                StringBuffer newSql = new StringBuffer();
                String[] split = sql.split("\\{.*?}");
                for (int i = 0; i < split.length; i++) {
                    newSql.append(split[i]).append("\"" + args[i] + "\"");
                }
                sql = newSql.toString();
            }

            // 获取方法返回类型，以及泛型类型
            Type genericReturnType = method.getGenericReturnType();
            Class<?> modelClass ;
            try {
                modelClass = (Class<?>) ((ParameterizedType) genericReturnType).getActualTypeArguments()[0];
            }catch (Exception e){
                modelClass = HashMap.class;
            }

            PreparedStatement statement = getStatement(sql, mybatisDatSource.getConnection());
            ResultSet result = statement.executeQuery();
            List list = parseResultSet(result, modelClass);


            LOG.debug("sql ===> " + sql);

            return list;
        }

        if (method.getName().equals("toString")) {
            return proxy.getClass().getInterfaces()[0].getName();
        }

        return null;
    }

    /**
     * 从 resultSet 里面获取对象，list
     * @param result
     * @param clz
     * @return
     */
    private List parseResultSet(ResultSet result, Class<?> clz) {
        List list = new ArrayList<>();
        try {
            Field[] fields = clz.getDeclaredFields();
            while (result.next()) {
                Object bean = clz.newInstance();
                if(bean instanceof Map){
                    // 没有泛型就返回 list<map>
                    Set<String> columnNames = getColumnNames(result);
                    for (String key : columnNames) {
                        Method method = clz.getMethod("put", Object.class, Object.class);

                        method.invoke(bean, key, result.getObject(key));
                    }
                }else {
                    Map<String, String> entityColmun = entityColmun(clz);
                    // 有泛型,就生成对应类型的对象
                    for (Field field : fields) {
                        String name = field.getName();

                        // 有字段加上了 @Transient  注解之后， entityColmun 里面就没有了，所以这里得直接跳过
                        String colmun = entityColmun.get(name);
                        if(colmun==null){
                            continue;
                        }

                        Object value = result.getObject(colmun);

                        Method method = clz.getMethod("set" + name.substring(0, 1).toUpperCase() + name.substring(1), field.getType());
                        method.invoke(bean, value);
                    }
                }
                list.add(bean);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
        return list;
    }

    /**
     * 获取表头，从 resultSet 里面获取
     * @param resultSet
     * @return
     */
    private Set<String> getColumnNames(ResultSet resultSet){
        LinkedHashSet<String> set = new LinkedHashSet<>();
        try {
            int count = resultSet.getMetaData().getColumnCount();
            for (int i = 1; i <= count ; i++) {
                set.add(resultSet.getMetaData().getColumnName(i));
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return set;
    }

    /**
     * 获取实体字段名=> 数据库的字段名
     *
     * @return
     */
    private Map<String, String> entityColmun(Class<?> clz) {
        HashMap<String, String> rowMap = new LinkedHashMap<>();
        Field[] fields = clz.getDeclaredFields();

        for (Field field : fields) {
            if (field.isAnnotationPresent(Transient.class)) {
                continue;
            }


            if (field.isAnnotationPresent(Column.class)) {
                rowMap.put(field.getName(), field.getAnnotation(Column.class).name());
                // 该字段是 ID，特殊标记
                if (field.isAnnotationPresent(Id.class)) {
                    rowMap.put(Id.class.getName(), field.getAnnotation(Column.class).name());
                }
            } else {
                rowMap.put(field.getName(), field.getName());
                if (field.isAnnotationPresent(Id.class)) {
                    rowMap.put(Id.class.getName(), field.getName());
                }
            }
        }
        return rowMap;
    }

    private PreparedStatement getStatement(String sql, Connection conn,
                                           Object... args) throws SQLException {
        LOG.debug("SQL：" + sql);
        PreparedStatement statement = conn.prepareStatement(sql, Statement.RETURN_GENERATED_KEYS);
        int i = 1;
        for (Object object : args) {
            statement.setObject(i, object);
            ++i;
        }
        return statement;
    }

    /**
     * 返回数据
     * @return
     */
    private List selectResult(String sql){
        return null;
    }


    /**
     * 释放数据库连接资源
     */
    public void close(AutoCloseable... closeables) {
        try {
            for (AutoCloseable closeable : closeables) {
                closeable.close();
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }


}
```