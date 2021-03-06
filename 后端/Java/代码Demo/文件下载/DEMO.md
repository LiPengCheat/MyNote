##### 下载resource目录下的文件

```java
public void download(Integer fileType, HttpServletResponse response) {
        CheckUtil.isEmptyWithEx(fileType, "下载文件类型不能为空");
        Map<Integer, String> fileMap = new HashMap<>(16);
        fileMap.put(1, "file/三羊马指令导入模板.xlsx");
        fileMap.put(2, "file/装箱计划.xls");
        fileMap.put(3, "file/装车导入模板.xlsx");
        final String exMsg = "根据类型下载文件失败";
        Resource resource = new ClassPathResource(fileMap.get(fileType));

        // 实现文件下载
        byte[] buffer = new byte[1024];
        try(InputStream fis = new FileInputStream(resource.getFile());
            BufferedInputStream bis = new BufferedInputStream(fis)) {
            // 配置文件下载
            response.setHeader("content-type", "application/octet-stream");
            response.setContentType("application/octet-stream");
            // 下载文件能正常显示中文
            response.setHeader("Content-Disposition", "attachment;filename=" + URLEncoder.encode(resource.getFilename(), "UTF-8"));
            OutputStream os = response.getOutputStream();
            int i = bis.read(buffer);
            while (i != -1) {
                os.write(buffer, 0, i);
                i = bis.read(buffer);
            }
        } catch (Exception e) {
            log.error(exMsg, e);
        }
    }
```

