# zk实现分布式锁

> 实现原理

使用zk的临时节点，然后监听该节点，在需要锁的时候注册临时节点，使用完毕之后删除该节点，调用监听的方法

> 参考文章：

https://blog.csdn.net/liyiming2017/article/details/83786331

> 实例代码：

    package zzq.zk;

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


        // 监听器
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
                        // 不为null的时候证明前一个节点还在，则等待，唤醒之后继续获取锁
                        synchronized (lockWatcher){
                            this.wait();
                        }
                        attemptLock();
                    }
                }

            }catch (Exception e){
                e.printStackTrace();
            }
        }

        public void releaseLock(){
            try {
                zooKeeper.delete(lockPath , -1);
                zooKeeper.close();
                logger.info(Thread.currentThread().getName() + " , 释放锁 =>" + lockPath );
            } catch (InterruptedException e) {
                e.printStackTrace();
            } catch (KeeperException e) {
                e.printStackTrace();
            }
        }
    }


> 调用例子：

    public class ZkLockTest implements Runnable {

        public static void main(String[] args) {
            ZkLockTest lockTest = new ZkLockTest();
            ExecutorService service = Executors.newCachedThreadPool();
            for (int i = 0; i < 100; i++) {
                service.submit(lockTest);
            }

        }

        private int count = 100;

        @Override
        public void run() {
            ZkLock lock = new ZkLock("192.168.31.211:2181,192.168.31.103:2181,192.168.31.23:2181",
                    10 * 1000, "/zklock");

            lock.lock();

            count--;

            System.err.println(Thread.currentThread().getName() + "==========>" + count);

            lock.releaseLock();
        }
    }