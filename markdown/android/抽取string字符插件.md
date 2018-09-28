## 自动抽取strings.xml 实现思路：

### 如何获取strings.xml中配置的所有字符串？
 在gradle编译过程中，会将所有资源内容（包括引入的module，aar）等,全部 copy 到 build 目录下，生成对应的 values.xml文件:


![value文件路径](https://github.com/zhaoyubetter/MarkdownPhotos/raw/master/img/build_values-.png)

![value文件路径](https://github.com/zhaoyubetter/MarkdownPhotos/raw/master/img/plugin_extract_strings.jpg)
