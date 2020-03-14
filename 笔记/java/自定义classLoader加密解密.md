# 自定义classLoader 解密加载器

### 加密代码

    package classloader;

    import java.io.File;
    import java.io.FileInputStream;
    import java.io.FileOutputStream;

    /**
    * 单个文件加密
    */
    public class EncryptCLassFile extends EncryptClass {

        /**
        * 单个.class文件加密
        *
        * @param source 源文件路径
        * @param target 加密后的文件路径
        */
        public EncryptCLassFile(String source, String target) {
            super();
            super.source = source;
            super.target = target;
            if(!new File(target).exists()){
                new File(target).getParentFile().mkdirs();
            }
        }

        @Override
        public void encrypt() {
            try {
                FileInputStream inputStream = new FileInputStream(source);
                FileOutputStream outputStream = new FileOutputStream(target);

                int len = 0;
                while ((len = inputStream.read()) != -1) {
                    outputStream.write(len^99);
                }
                outputStream.flush();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }

### 解密classLoader

    package classloader;

    import java.io.ByteArrayOutputStream;
    import java.io.FileInputStream;

    /**
    * 加载加密的class文件为 class对象
    */
    public class DecryptClassLoader extends ClassLoader {

        private String rootpath;

        public DecryptClassLoader(String rootpath) {
            this.rootpath = rootpath;
        }

        @Override
        public Class<?> findClass(String name) throws ClassNotFoundException {
            // 先查询有没有加载过这个类
            Class<?> c = super.findLoadedClass(name);

            if (c != null) {
                return c;
            } else {

                // 父类加载器也加载不到，就从文件路径加载
                byte[] classData = getClassData(name);

                c = defineClass(name, classData, 0, classData.length);

            }

            return c;
        }

        /**
        * 加载.class 文件
        *
        * @param className
        * @return
        */
        private byte[] getClassData(String className) {
            String path = rootpath + "/" + className.replace('.', '/') + ".class";
            try {
                ByteArrayOutputStream byteArrayOutputStream = new ByteArrayOutputStream();

                FileInputStream fileInputStream = new FileInputStream(path);

                int len = 0;
                while ((len = fileInputStream.read()) != -1) {
                    byteArrayOutputStream.write(len^99);
                }

                return byteArrayOutputStream.toByteArray();
            } catch (Exception e) {
                e.printStackTrace();
                return null;
            }
        }

    }

### 调用示例

    @Test
    public void encryptClassTest() throws ClassNotFoundException {
        EncryptCLassFile encryptCLassFile = new EncryptCLassFile("D:\\githubWorkSpace\\pkusoft\\target\\classes\\com\\zzq\\util\\JsonResult.class", "D:\\gc\\com\\zzq\\util\\JsonResult.class");
        encryptCLassFile.encrypt();

        DecryptClassLoader decryptClassLoader = new DecryptClassLoader("D://gc//");

        Class<?> aClass = decryptClassLoader.findClass("com.zzq.util.JsonResult");


    }