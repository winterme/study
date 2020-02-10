# webp格式图片转jpg


jar: 在该文件夹中

webp-imageio-core-0.1.0.jar

webp转jpg



        String inputWebpPath = "D:\\githubWorkSpace\\upload\\pdf\\1.webp";
        String outputJpgPath = "D:\\githubWorkSpace\\upload\\pdf\\test_pic/test_.jpg";
        String outputJpegPath = "D:\\githubWorkSpace\\upload\\pdf\\test_pic/test_.jpeg";
        String outputPngPath = "D:\\githubWorkSpace\\upload\\pdf\\test_pic/test_.png";
        ImageReader reader = (ImageReader)ImageIO.getImageReadersByMIMEType("image/webp").next();
        WebPReadParam readParam = new WebPReadParam();
        readParam.setBypassFiltering(true);
        reader.setInput(new FileImageInputStream(new File(inputWebpPath)));
        BufferedImage image = reader.read(0, readParam);
        ImageIO.write(image, "png", new File(outputPngPath));
        ImageIO.write(image, "jpg", new File(outputJpgPath));
        ImageIO.write(image, "jpeg", new File(outputJpegPath));

jpg 转 webp

        String inputPngPath = "test_pic/test.png";
        String inputJpgPath = "test_pic/test.jpg";
        String outputWebpPath = "test_pic/test_.webp";
        BufferedImage image = ImageIO.read(new File(inputJpgPath));
        ImageWriter writer = (ImageWriter)ImageIO.getImageWritersByMIMEType("image/webp").next();
        WebPWriteParam writeParam = new WebPWriteParam(writer.getLocale());
        writeParam.setCompressionMode(1);
        writer.setOutput(new FileImageOutputStream(new File(outputWebpPath)));
        writer.write((IIOMetadata)null, new IIOImage(image, (List)null, (IIOMetadata)null), writeParam);

