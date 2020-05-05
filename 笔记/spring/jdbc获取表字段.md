# jdbc获取表字段

## 1. mybatis 里面 xml 获取

直接查 数据库系统表，但是这个可能账号不是有权限的。

mysql 和 oracle 不一样

```
<select id="getColumns" resultType="com.pkusoft.kernelsupport.dao.kernelsupport.po.TableColunm">
    <choose>
        <when test="_databaseId == 'MySQL'">
            SELECT
            COLUMN_NAME as columnName,
            DATA_TYPE as dataType
            FROM
            information_schema.COLUMNS
            WHERE
            table_name = #{tableName , jdbcType=VARCHAR}
        </when>
        <when test="_databaseId == 'Oracle'">
            SELECT
            A.COLUMN_NAME as columnName,
            A.DATA_TYPE as dataType
            FROM
            USER_TAB_COLUMNS A
            WHERE
            TABLE_NAME = #{tableName , jdbcType=VARCHAR}
        </when>
    </choose>
</select>
```

## 2. jdbc 获取

```
@Qualifier(value = "dataSource0")
@Autowired
private DruidDataSource druidDataSource;


PreparedStatement statement = getStatement("select * FROM SYS_DATA_OWNER_DEPT where DEPT_ID='-1' ", (Connection) (druidDataSource.getConnection()));
ResultSet resultSet = statement.executeQuery();
columnNames = getColumnNames(resultSet);

PreparedStatement statement = conn.prepareStatement(sql, Statement.RETURN_GENERATED_KEYS);


	/**
     * 获取表头，从 resultSet 里面获取
     *
     * @param resultSet
     * @return
     */
    private LinkedHashSet<String> getColumnNames(ResultSet resultSet) {
        LinkedHashSet<String> set = new LinkedHashSet<>();
        try {
            int count = resultSet.getMetaData().getColumnCount();
            for (int i = 1; i <= count; i++) {
                set.add(resultSet.getMetaData().getColumnName(i));
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return set;
    }

```

## 3. 从 meteData 里面获取

```
ResultSet rs = null;
try {
    rs = dataSource.getConnection().getMetaData().getColumns(null, null, "SYS_DATA_OWNER_DEPT", null);
    while (rs.next()) {
        String column = rs.getString("COLUMN_NAME");
        column = column.toUpperCase();
        // 过滤归属单位字段OWNER_DEPT_*
        if (column.startsWith("OWNER_DEPT_")) {
            ownerDeptColumnNames.add(column);
        }
    }
} finally {
    if (rs != null) {
        rs.close();
    }
}
```