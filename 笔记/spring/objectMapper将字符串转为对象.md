# Object Mapper 将字符串转为 对象

## 转单对象，比如说 Map,UserInfo ...

```
mapper.readValue(value,Map.class)
```

## 转为 List<?> 泛型对象

### 1. 

```
objectMapper.readValue(value, new TypeReference<List<SysDicItem>>() {
});
```

### 2.

```
JavaType javaType = mapper.getTypeFactory().constructParametricType(ArrayList.class, RoleMemuPrivilegeVo.class);
ArrayList<RoleMemuPrivilegeVo> list = mapper.readValue(value, javaType)
```