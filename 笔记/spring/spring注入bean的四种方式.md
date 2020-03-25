# spring注入bean的四种方式

## 1. implements FactoryBean

实现此类，bean 在被使用的时候才直接 getObject() 方法。懒加载

```java
package com.zzq.mybatis.wiredBean;

import com.zzq.proxy.Animal;
import com.zzq.proxy.AnimalProxy;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.FactoryBean;
import org.springframework.stereotype.Component;

@Slf4j
@Component(value = "animalProxyFactoryBean")
public class AnimalProxyFactoryBean implements FactoryBean {

    private AnimalProxy animalProxy;

    @Override
    public Object getObject() throws Exception {
        log.info("AnimalProxyFactoryBean init...");

        animalProxy = new AnimalProxy(new Animal() {
            @Override
            public void sayhi(String msg) {
                System.out.println("===LCM===" + msg);
            }

            @Override
            public void eatSomething() {
                System.out.println("===ZZQ===");
            }
        });
        return animalProxy;
    }

    @Override
    public Class<?> getObjectType() {
        return Animal.class;
    }
}
```

## 2. AtBean

```java
package com.zzq.mybatis.wiredBean;

import com.zzq.proxy.Animal;
import com.zzq.proxy.AnimalProxy;
import lombok.extern.slf4j.Slf4j;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Slf4j
@Configuration
public class AtBean {

    @Bean
    public AnimalProxy setAnimalproxy() {

        log.info("animal init....");

        return new AnimalProxy(new Animal() {
            @Override
            public void sayhi(String msg) {
                System.out.println("===LCM===" + msg);
            }

            @Override
            public void eatSomething() {
                System.out.println("===ZZQ===");
            }
        });
    }
}
```

## 3. implements BeanFactoryPostProcessor

```java
package com.zzq.mybatis.wiredBean;

import com.zzq.proxy.Animal;
import com.zzq.proxy.AnimalInvocation;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.BeansException;
import org.springframework.beans.factory.BeanFactory;
import org.springframework.beans.factory.config.BeanFactoryPostProcessor;
import org.springframework.beans.factory.config.ConfigurableListableBeanFactory;
import org.springframework.context.annotation.Configuration;

import java.util.Iterator;

@Slf4j
@Configuration
public class BeanFactoryPostProcessorTest implements BeanFactoryPostProcessor {

    private BeanFactory beanFactory;

    @Override
    public void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) throws BeansException {
        // beanFactory 可以手动注入bean，但是没有 BeanDefinitionRegistry 好用
        beanFactory.registerSingleton("CAT-TEST", new AnimalInvocation(new Animal() {
            @Override
            public void sayhi(String msg) {
                System.out.println("不知名的东西，不知道怎么打招呼....");
            }

            @Override
            public void eatSomething() {
                System.out.println("因为不知名，所以不知道吃什么....");
            }
        }));
        Iterator<String> beanNamesIterator = beanFactory.getBeanNamesIterator();
        while (beanNamesIterator.hasNext()) {
            log.info(beanNamesIterator.next());
        }

        this.beanFactory = beanFactory;
    }

    public <T> T getBean(String beanName){
        return (T)this.beanFactory.getBean(beanName);
    }

    public <T> T getBean(Class clz){
        return (T)this.beanFactory.getBean(clz);
    }

}
```

## 4. implements ImportBeanDefinitionRegistrar

继承这个类的好处就是，方法自带 AnnotationMetadata，目标注解加上 @Import(AutoConfigurationImport.class)，在 方法里面就能直接获取到

```java
package com.zzq.mybatis.wiredBean;

import com.zzq.mybatis.PkuInvocationHandle;
import com.zzq.mybatis.PkuSession;
import com.zzq.mybatis.annotation.Mapper;
import com.zzq.mybatis.annotation.MapperScanner;
import javassist.CannotCompileException;
import javassist.ClassPool;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.config.ConstructorArgumentValues;
import org.springframework.beans.factory.support.BeanDefinitionRegistry;
import org.springframework.beans.factory.support.RootBeanDefinition;
import org.springframework.context.annotation.ImportBeanDefinitionRegistrar;
import org.springframework.core.type.AnnotationMetadata;
import org.springframework.stereotype.Component;

import java.io.File;
import java.io.FileInputStream;
import java.io.IOException;
import java.util.HashMap;
import java.util.Map;

import static org.springframework.beans.factory.config.BeanDefinition.SCOPE_SINGLETON;

@Component
public class AutoConfigurationImport implements ImportBeanDefinitionRegistrar {

    private Logger log = LoggerFactory.getLogger(this.getClass());

    private void getAllMapper(Map<String, Class> mapper, File file, ClassPool classPool) throws IOException, CannotCompileException {
        for (File listFile : file.listFiles()) {
            if (listFile.isFile()) {
                String fileName = listFile.getName().substring(0, 1).toLowerCase() + listFile.getName().substring(1);
                fileName = fileName.substring(0,fileName.indexOf((char)46));
                mapper.put(fileName, classPool.makeClass(new FileInputStream(listFile)).toClass());
            }
            if (listFile.isDirectory()) {
                getAllMapper(mapper, listFile, classPool);
            }
        }
    }

    @Override
    public void registerBeanDefinitions(AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry) {
        // 解析 MapperScanner 注解里面需要扫描的包路径
        String package_path = null;
        for (String annotationType : importingClassMetadata.getAnnotationTypes()) {
            if(annotationType.equals(MapperScanner.class.getName())){
                package_path  = (String) importingClassMetadata.getAnnotationAttributes(annotationType).get("value");
            }
        }

        File file = new File(this.getClass().getResource("/").getFile() + package_path.replace('.','/'));
        Map<String, Class> classes = new HashMap<>();
        Map<String, Class> rootBeanClass = new HashMap<>();
        ClassPool classPool = ClassPool.getDefault();

        // 获取需要扫描的包下面的所有类
        try {
            getAllMapper(classes, file, classPool);
        } catch (Exception e) {
            e.printStackTrace();
        }

        // 开始解析扫描包下的 class对象，获取所有有 Mapper 注解覆盖的类
        classes.keySet().forEach(beanName -> {
            if (classes.get(beanName).isAnnotationPresent(Mapper.class)) {
                rootBeanClass.put(beanName, PkuSession.getPkuSession().getMapper(classes.get(beanName)).getClass());
            }
        });

        PkuInvocationHandle pkuInvocationHandle = new PkuInvocationHandle();
        // 开始注入
        rootBeanClass.keySet().forEach(beanName -> {
            RootBeanDefinition rootBeanDefinition = new RootBeanDefinition();

            // set beanClass => 此处填写的是jdl动态代理之后返回的class对象
            rootBeanDefinition.setBeanClass(rootBeanClass.get(beanName));
            rootBeanDefinition.setScope(SCOPE_SINGLETON);

            // 添加构造方法的参数，因为jdk动态代理生成的类需要 InvocationHandle 这个参数
            ConstructorArgumentValues constructorArgumentValues = new ConstructorArgumentValues();
            constructorArgumentValues.addGenericArgumentValue(pkuInvocationHandle);

            rootBeanDefinition.setConstructorArgumentValues(
                    constructorArgumentValues
            );

            registry.registerBeanDefinition(beanName, rootBeanDefinition);
            log.info(String.format("%s bean is autowired", beanName) );
        });

    }
}
```

## 5. 也可以implements BeanDefinitionRegistryPostProcessor