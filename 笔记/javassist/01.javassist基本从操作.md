# javassist基本从操作

运行期直接通过代码字符串来生成类
此处只放代码：

    package com.zzq.javassist;

    import javassist.*;

    import java.lang.reflect.Constructor;
    import java.lang.reflect.Method;

    public class ProxyRun {

        public static void main(String[] args) throws Exception {
            ClassPool classPool = ClassPool.getDefault();

            // 创建类
            CtClass clz = classPool.makeClass(ProxyRun.class.getPackage().getName() + ".Emp");

            // 创建字段
            CtField eid = CtField.make("private int eid;",clz);
            CtField name = CtField.make("private String name;",clz);

            // 添加字段
            clz.addField(eid);
            clz.addField(name);

            // 创建方法
            CtMethod getId = CtMethod.make("public int getEid(){return eid;}", clz);
            CtMethod getName = CtMethod.make("public String getName(){return name;}", clz);
            CtMethod setName = CtMethod.make("public void setName(String name){this.name = name;}", clz);

            // 添加方法
            clz.addMethod(getId);
            clz.addMethod(getName);
            clz.addMethod(setName);

            CtConstructor constructor = new CtConstructor(new CtClass[]{CtClass.intType , classPool.get("java.lang.String")} , clz );
            constructor.setBody("{this.eid = $1 ; this.name = $2;}");

            // 添加构造方法
            clz.addConstructor(constructor);

            CtConstructor noargs = new CtConstructor(new CtClass[]{}, clz);
            noargs.setBody("{}");
            clz.addConstructor(noargs);

            clz.writeFile(ProxyRun.class.getResource("/").getPath());

            Class<?> aClass = Class.forName(clz.getName());
            Constructor<?> argsCons = aClass.getConstructor(int.class, String.class);

            Object instance = argsCons.newInstance(25, "zzq");
            Method nameMethod = aClass.getMethod("getName");
            // 执行 getName 方法
            System.out.println(nameMethod.invoke(instance , null));

            // 获取 setName 方法
            Method setName2 = aClass.getMethod("setName", String.class);
            setName2.invoke(instance, "lcm");


            // 执行 getName 方法
            System.out.println(nameMethod.invoke(instance , null));


        }
    }
