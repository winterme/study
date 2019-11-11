# java操作zookeeper

![x](../images/zk-opt01.png)
![x](../images/zk-opt02.png)
![x](../images/zk-opt03.png)
> 异步方式代码示例

        zooKeeper.create(basePath + "/sindex",
                "s".getBytes(), ZooDefs.Ids.OPEN_ACL_UNSAFE, CreateMode.PERSISTENT,
                new AsyncCallback.StringCallback() {
                    @Override
                    public void processResult(int rc, String path, Object ctx, String name) {
                        System.out.println(rc);
                        System.out.println(ctx);
                        System.out.println(name);
                    }
                }, "/ctx---");

# zookeeper数据格式

![x](../images/zk-datamodel-01.png)

# zookeeper 设计目标
![x](../images/zk-desg-01.png)

# zk异步回调
![x](../images/zk-async.png)

> 例子

![x](../images/zk-async-t.png)