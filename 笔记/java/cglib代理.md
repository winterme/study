# cglib代理

    public static void main(String[] args) {

        Enhancer enhancer = new Enhancer();

        // 增强父类，cglib 是继承父类来的
        enhancer.setSuperclass(Cat.class);

        // 设置代理名称
        enhancer.setNamingPolicy(SpringNamingPolicy.INSTANCE);

        // 过滤方法
        enhancer.setCallback(new InvocationHandler() {
            @Override
            public Object invoke(Object o, Method method, Object[] objects) throws Throwable {
                log.info("cglib 开始工作了....");

                return method.invoke(o,objects);
            }
        });

        Cat cat = (Cat) enhancer.create();

        cat.sayHi("哈哈哈哈");


    }
