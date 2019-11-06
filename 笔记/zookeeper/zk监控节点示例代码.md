# zk监控节点示例代码

    package zzq.zk;

    import org.apache.zookeeper.*;
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;

    import java.util.List;
    import java.util.concurrent.CountDownLatch;

    public class MyZkClient {

        private static String CONNECTION_STR = "192.168.31.211:2181,192.168.31.103:2181,192.168.31.23:2181";

        private int SESSION_TIMEOUT = 10*1000;

        private static Logger logger = LoggerFactory.getLogger(MyZkClient.class);

        private ZooKeeper zooKeeper;

        public MyZkClient(){
            try {
                CountDownLatch latch = new CountDownLatch(1);

                Watcher watcher = new Watcher() {
                    @Override
                    public void process(WatchedEvent event) {
                        //todo
                        System.out.println("发生变化");
                        latch.countDown();
                        getChlid();
                    }
                };
                zooKeeper = new ZooKeeper(CONNECTION_STR , SESSION_TIMEOUT , watcher);
                latch.await();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }

        public static void main(String[] args) throws InterruptedException {
            MyZkClient client = new MyZkClient();

            Thread.sleep(Integer.MAX_VALUE);
        }

        public void getChlid(){
            try {
                System.out.println("===============================================================");
                List<String> children = this.zooKeeper.getChildren("/server", true);
                for (String child : children) {
                    System.out.println( child );
                }
                System.out.println("===============================================================");
            } catch (KeeperException e) {
                e.printStackTrace();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }

    }
