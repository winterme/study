# jdk 动态代理

代理创建：

public static <T> T getProxy(Animal animal){
        Class[] classes = animal.getClass().getInterfaces();

        return (T)Proxy.newProxyInstance(Animal.class.getClassLoader() , classes , new AnimalInvocation(animal));
    }

参数解析：

Proxy.newProxyInstance(Animal.class.getClassLoader() , classes , new AnimalInvocation(animal))    

第一个参数，代理对象的 class的classLoader
第二个参数，代理对象的父类接口，可以多个
第三个参数，实现了 java.lang.reflect.InvocationHandler 类


代码示例：

    import java.lang.reflect.InvocationHandler;
    import java.lang.reflect.Method;

    public class AnimalInvocation implements InvocationHandler {

        private Animal animal;

        public AnimalInvocation(Animal animal) {
            this.animal = animal;
        }

        @Override
        public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
            System.out.println("调用之前.....");
            Object result = method.invoke(animal, args);
            System.out.println("调用之后.....");
            return result;
        }
    }


### jdk动态代理之后生成的类，该类不在文件中，字节码操作

    //
    // Source code recreated from a .class file by IntelliJ IDEA
    // (powered by Fernflower decompiler)
    //

    import com.zzq.proxy.Animal;
    import java.lang.reflect.InvocationHandler;
    import java.lang.reflect.Method;
    import java.lang.reflect.Proxy;
    import java.lang.reflect.UndeclaredThrowableException;

    public final class Cat extends Proxy implements Animal {
        private static Method m1;
        private static Method m3;
        private static Method m4;
        private static Method m2;
        private static Method m0;

        public Cat(InvocationHandler var1) throws  {
            super(var1);
        }

        public final boolean equals(Object var1) throws  {
            try {
                return (Boolean)super.h.invoke(this, m1, new Object[]{var1});
            } catch (RuntimeException | Error var3) {
                throw var3;
            } catch (Throwable var4) {
                throw new UndeclaredThrowableException(var4);
            }
        }

        public final void eatSomething() throws  {
            try {
                super.h.invoke(this, m3, (Object[])null);
            } catch (RuntimeException | Error var2) {
                throw var2;
            } catch (Throwable var3) {
                throw new UndeclaredThrowableException(var3);
            }
        }

        public final void sayhi(String var1) throws  {
            try {
                super.h.invoke(this, m4, new Object[]{var1});
            } catch (RuntimeException | Error var3) {
                throw var3;
            } catch (Throwable var4) {
                throw new UndeclaredThrowableException(var4);
            }
        }

        public final String toString() throws  {
            try {
                return (String)super.h.invoke(this, m2, (Object[])null);
            } catch (RuntimeException | Error var2) {
                throw var2;
            } catch (Throwable var3) {
                throw new UndeclaredThrowableException(var3);
            }
        }

        public final int hashCode() throws  {
            try {
                return (Integer)super.h.invoke(this, m0, (Object[])null);
            } catch (RuntimeException | Error var2) {
                throw var2;
            } catch (Throwable var3) {
                throw new UndeclaredThrowableException(var3);
            }
        }

        static {
            try {
                m1 = Class.forName("java.lang.Object").getMethod("equals", Class.forName("java.lang.Object"));
                m3 = Class.forName("com.zzq.proxy.Animal").getMethod("eatSomething");
                m4 = Class.forName("com.zzq.proxy.Animal").getMethod("sayhi", Class.forName("java.lang.String"));
                m2 = Class.forName("java.lang.Object").getMethod("toString");
                m0 = Class.forName("java.lang.Object").getMethod("hashCode");
            } catch (NoSuchMethodException var2) {
                throw new NoSuchMethodError(var2.getMessage());
            } catch (ClassNotFoundException var3) {
                throw new NoClassDefFoundError(var3.getMessage());
            }
        }
    }
