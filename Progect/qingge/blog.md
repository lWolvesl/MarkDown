# blog

## 三层架构

- controller引入service
- service引入mapper

## Mybatisplus

- 若数据库表中存在_,会自动转换为驼峰，如nick_name =》nickName

## hutool工具类

## VUE

```
npm install @element-plus/icons
```

### 上传器

elementui

后端接口

```java
@RestController
@RequestMapping("/files")
public class FileController {
    @PostMapping("/upload")
    public Result<?> upload(MultipartFile file) throws IOException {
        String name = file.getOriginalFilename();
        String rootFilePath = System.getProperty("user.dir") + "/src/main/resources/files/" + IdUtil.fastSimpleUUID() + "_" + name;
        FileUtil.writeBytes(file.getBytes(),rootFilePath);
        return Result.success(ip + ":" + port + "/files/" + flag + "_" + name);
    }
}

```

下载

```java
OutputStream os;
String bashPath = System.getProperty("user.dir") + "/src/main/resources/files/";
List<String> fileNames = FileUtil.listFileNames(bashPath);  //获取根路径下所有文件名称
String avatar = fileNames.stream().filter(name -> name.contains(flag)).findAny().orElse(""); //找到文件
try {
    if (StrUtil.isNotEmpty(avatar)) {
        response.addHeader("Content-Disponsition", "attachment;filename=" + URLEncoder.encode(avatar, "UTF-8"));
        response.setContentType("application/octet-stream");
        byte[] bytes = FileUtil.readBytes(bashPath + avatar);
        os = response.getOutputStream();
        os.write(bytes);
        os.flush();
        os.close();
    }
} catch (Exception e) {
    System.out.println("失败");
}
```

