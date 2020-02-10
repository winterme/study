# java-将图片转成pdf

1. pom

        <!-- https://mvnrepository.com/artifact/com.itextpdf/itextpdf -->
        <dependency>
            <groupId>com.itextpdf</groupId>
            <artifactId>itextpdf</artifactId>
            <version>5.5.13.1</version>
        </dependency>

2. 代码示例

        /*** @param picturePath 图片地址*/
        private static void createPic(Document document, String picturePath) {
            try {
                Image image = Image.getInstance(picturePath);
                float documentWidth = document.getPageSize().getWidth() - document.leftMargin() - document.rightMargin();
                float documentHeight = documentWidth / 800 * 1128;//重新设置宽高
                image.scaleAbsolute(documentWidth, documentHeight);//重新设置宽高
    //            image.scaleAbsolute(800f, 1128f);//重新设置宽高
                document.add(image);
            } catch (Exception ex) {
            }
        }

        @Test
        public void m22222() throws Exception {

            image2pdf("D:\\githubWorkSpace\\upload\\pdf\\121", "D:\\githubWorkSpace\\upload\\pdf\\121.pdf");
            image2pdf("D:\\githubWorkSpace\\upload\\pdf\\122", "D:\\githubWorkSpace\\upload\\pdf\\122.pdf");

    //        System.out.println(line.substring(0,line.indexOf("\\.")));

            /*image2pdf("D:\\githubWorkSpace\\upload\\pdf\\61\\" , "D:\\githubWorkSpace\\upload\\pdf\\61.pdf");
            image2pdf("D:\\githubWorkSpace\\upload\\pdf\\62\\" , "D:\\githubWorkSpace\\upload\\pdf\\62.pdf");
            image2pdf("D:\\githubWorkSpace\\upload\\pdf\\71\\" , "D:\\githubWorkSpace\\upload\\pdf\\71.pdf");
            image2pdf("D:\\githubWorkSpace\\upload\\pdf\\72\\" , "D:\\githubWorkSpace\\upload\\pdf\\72.pdf");*/
        }

        public static void image2pdf(String text, String pdf) throws DocumentException, IOException {
            Document document = new Document(PageSize.A4);
            OutputStream os = new FileOutputStream(new File(pdf));
            PdfWriter.getInstance(document,os);
            document.open();

            File file = new File(text);
            File[] files = file.listFiles();
            for (int i = 0; i < files.length; i++) {
                for (int j = i+1; j < files.length; j++) {
                    String f1 = files[i].getName();
                    int f1num = Integer.valueOf(f1.substring(0,f1.indexOf((char)46)));

                    String f2 = files[j].getName();
                    int f2num = Integer.valueOf(f2.substring(0,f2.indexOf((char)46)));

                    if(f1num>f2num){
                        File temp = files[i];

                        files[i] = files[j];
                        files[j] = temp;
                    }
                }
            }

            for (File listFile : files) {
                createPic(document , listFile.getAbsolutePath());
            }


            document.close();
        }