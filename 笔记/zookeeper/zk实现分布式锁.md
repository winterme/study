# zk实现分布式锁

https://blog.csdn.net/liyiming2017/article/details/83786331

    package zk;

    import org.apache.zookeeper.*;
    import org.apache.zookeeper.data.Stat;
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;

    import java.util.ArrayList;
    import java.util.Collections;
    import java.util.List;
    import java.util.UUID;
    import java.util.concurrent.CountDownLatch;

    public class ZkLock {

        private static Logger logger = LoggerFactory.getLogger(ZkLock.class);

        private String connectionStr ;
        private int sessionTimeOut;
        private ZooKeeper zooKeeper;
        private boolean initFlag = true;
        private String lockRootPath ;
        private String lockPath;
        private String lockNodeName = "lock_";


        //
        private Watcher lockWatcher = new Watcher() {
            @Override
            public void process(WatchedEvent event) {
                logger.info(event.getPath()+" 锁释放！");
                synchronized (this){
                    notifyAll();
                }
            }
        };

        public ZooKeeper getZooKeeper() {
            return zooKeeper;
        }

        public ZkLock(String connectionStr, int sessionTimeOut , String lockRootPath) {
            this.connectionStr = connectionStr;
            this.sessionTimeOut = sessionTimeOut;
            this.lockRootPath = lockRootPath;

            init();
        }

        private void init(){
            try{
                CountDownLatch latch = new CountDownLatch(1);

                Watcher watcher = new Watcher() {
                    @Override
                    public void process(WatchedEvent event) {
                        if(initFlag){
                            if(event.getState()== Event.KeeperState.SyncConnected){
                                logger.info(connectionStr + "======>连接成功！");
                                initFlag = false;
                                latch.countDown();
                            }
                        }
                    }
                };

                zooKeeper = new ZooKeeper(connectionStr, sessionTimeOut, watcher);

                latch.await();
            }catch (Exception e){
                e.printStackTrace();
            }
        }


        public void lock(){
            // 创建锁，临时节点
            createLock();
        }

        /**
        * 创建锁
        */
        private void createLock(){
            try{
                Stat exists = zooKeeper.exists(lockRootPath, false);
                if(exists==null){
                    zooKeeper.create(lockRootPath, "locl".getBytes(), ZooDefs.Ids.OPEN_ACL_UNSAFE, CreateMode.PERSISTENT);
                }

                String lpath = zooKeeper.create(lockRootPath+"/"+lockNodeName, "t".getBytes(), ZooDefs.Ids.OPEN_ACL_UNSAFE, CreateMode.EPHEMERAL_SEQUENTIAL);
                logger.info(Thread.currentThread().getName() + " 创建锁，路径=>" + lpath );

                this.lockPath = lpath;
            }catch (Exception e){

            }
        }

        /**
        * 获得锁
        */
        private void attemptLock(){
            try{
                // 获取根节点下的所有子节点
                List<String> children = zooKeeper.getChildren(lockRootPath, false);

                // 排序
                Collections.sort(children);

                // 创建出来的节点是这样的 => /zklock/lock_0000000002  除去 lockRootPath(/zklock) + 1 之后就是 lock_0000000002 子路径
                int index = children.indexOf( lockPath.substring( lockRootPath.length()+1 )  );

                // 第一个获取锁
                if( index==0 ){
                    logger.info(Thread.currentThread().getName()+" 获取锁，lockPath=> " + lockPath);
                    return;
                }else{
                    // 监听前一个
                    Stat stat = zooKeeper.exists(children.get(index - 1), lockWatcher);

                    // 前一个节点执行完了，掉线了，重新获取锁
                    if(stat==null){
                        attemptLock();
                    }else{
                        
                    }
                }

            }catch (Exception e){
                e.printStackTrace();
            }
        }

        public static void main(String[] args) {
            ArrayList<String> list = new ArrayList<>();
            list.add("lock_0000000002");
            list.add("lock_0000000003");
            list.add("lock_0000000004");

            String lockPath = "/zklock/lock_0000000002";
            String rootLockPath = "/zklock";


            System.out.println(list.indexOf(lockPath.substring(rootLockPath.length() + 1)));
            System.out.println( lockPath.substring(rootLockPath.length() + 1) );
            int i = lockPath.indexOf(lockPath.substring(rootLockPath.length() + 1));

            System.out.println(i);

        }
    }
