---
title: alibaba开源EasyExcel
date: 2020-04-27 01:57:22
updated: 2021-05-16 01:50:56
description: EasyExcel是一个基于Java的简单、省内存的读写Excel的开源项目。在尽可能节约内存的情况下支持读写百M的Excel
tags:
    - SpringBoot
# 置顶优先级 数值越大优先级越高 #
sticky: 9990
cover: https://i.loli.net/2021/06/19/UWHoY7tp2Nd3qwf.jpg
# 【可选】文章分类 #
categories: 工具
# 【可选】文章关键字 #
keywords:
    - excel
---

> EasyExcel是一个基于Java的简单、省内存的读写Excel的开源项目。在尽可能节约内存的情况下支持读写百M的Excel。

{% tabs windows %}
<!-- tab 官方github地址@fab fa-github-->

{% btn 'https://github.com/alibaba/easyexcel',官方github地址,far fa-hand-point-right,green larger %}

<!-- endtab -->

<!-- tab 个人Demo地址@fab fa-github -->

{% btn 'https://github.com/Endless-zby/EasyExcel-Demo',个人Demo地址,far fa-hand-point-right,green larger %}
- (新加入web中的读和写功能，集成swagger，默认地址 [http://127.0.0.1:8081/swagger-ui.html](http://127.0.0.1:8081/swagger-ui.html) )

<!-- endtab -->
{% endtabs %}


# EasyExcel解决了什么

我们以使用最多的 `Apache poi` 来做为对比

![](https://www.zby123.club/wp-content/uploads/2020/04/easyExcel-1024x600.png)

- 这是我项目中使用到的 poi 做Excel的解析，就这一堆看着难受不难受

- 可以看出直接使用poi的结果就是繁琐，代码看着也不舒服，最为致命的是很容易造成OOM，内存占用是poi永远不可避免的话题，虽然EasyExcel底层还是使用poi，但是做了很多的优化，尤其解决了并发处理中的bug（说这么多干嘛！其实我只需要代码看着简洁易懂）

# SpringBoot

## 引入jar包

```xml
        <!--Alibaba-Excel-->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>easyexcel</artifactId>
            <version>2.2.2</version>
        </dependency>
```

- 我使用的最新2.2.2版本，对比csdn等博客中记录的1.0之后的版本来说，这个版本已经进行了很多次的迭代，修复了许多bug，而2.2.1是最近的一个正式版。
# 能干什么
## 三大功能（读、写、填充）

### 读（Read）

*   示例Excel

![](https://www.zby123.club/wp-content/uploads/2020/04/easyExcel1.png)

- 以下Demo都以这个Excel为例子

*   创建实体类 （代码）

![](https://www.zby123.club/wp-content/uploads/2020/04/easyExcel2-1024x586.png)

图中的converter属性，后面再介绍

```java
@Component
@AllArgsConstructor //全参构造器
@NoArgsConstructor  //无参构造
@Builder            //作用于类上，将类转变为建造者模式
public @Data class Student {

    @ExcelProperty(value = "姓名")
    @NonNull private String name;

    @ExcelProperty(value = "年龄")
    private Integer age;

    @ExcelProperty(value = "性别")
    private String gender;

    @ExcelProperty(value = "身高")
    private String height;

    @ExcelProperty(value = "体重")
    private String weight;
    /**
     * 自定义转换器（String to String）
     */
    @ExcelProperty(value = "专业",converter = CustomStringStringConverter.class)
    private String major;

    @DateTimeFormat("yyyy-MM-dd HH:mm:ss")
    @ExcelProperty(value = "日期")
    private String update;

}
```

*   监听器

```java
/**
 * @Author: 赵博雅
 * @Date: 2020/4/26 18:57
 */
public class ListenerExcel extends AnalysisEventListener<Student> {
    private static final Logger LOGGER = LoggerFactory.getLogger(ListenerExcel.class);

    //批次保存最大值
    private static final int BATCH_COUNT = 2;

    List<Student> list = new ArrayList<>();

    private StudentDao studentDao;

    /**
     * 如果使用了spring,请使用这个构造方法。每次创建Listener的时候需要把spring管理的类传进来
     *
     * @param studentDao
     */
    public ListenerExcel(StudentDao studentDao) {
        this.studentDao = studentDao;
    }

    /**
     * 这个每一条数据解析都会来调用
     *
     * @param data
     *            one row value. Is is same as invoke
     * @param context
     */
    @Override
    public void invoke(Student data, AnalysisContext context) {
        Integer rowNum = context.getCurrentRowNum();
        if (rowNum == 0) {
            return;
        }
        LOGGER.info("解析到一条数据:{},位于第{}行", JSON.toJSONString(data), rowNum);

        list.add(data);
        // 达到BATCH_COUNT了，需要去存储一次数据库，防止数据几万条数据在内存，容易OOM
        if (list.size() >= BATCH_COUNT) {
            saveData();
            // 存储完成清理 list
            list.clear();
        }
    }

    /**
     * 所有数据解析完成了 都会来调用
     *
     * @param context
     */
    @Override
    public void doAfterAllAnalysed(AnalysisContext context) {
        // 这里也要保存数据，确保最后遗留的数据也存储到数据库
        saveData();
        LOGGER.info("所有数据解析完成！");
    }

    /**
     * 加上存储数据库
     */
    private void saveData() {
        LOGGER.info("{}条数据，开始存储数据库！", list.size());
        studentDao.saveAll(list);
        LOGGER.info("存储数据库成功！");
    }
}
```
#### 重点
> 争对我们每个要解析的Excel文件都应实现一个监听器，实际就是简化版的解析器，其中我们只需要实现`invoke()`和`doAfterAllAnalysed()`两个方法，`invoke`是Excel文件中每解析一行都需要执行的操作，如果用在我们实际的业务中，一般会放入集合中等待入表或做算法操作，`doAfterAllAnalysed`将在整个sheet解析完成后执行。

> 注意：如果是Spring的项目中使用，Excel的监听器不能被Spring所管理， 要每次读取excel都要new，里面用到spring的话可以构造方法传进去 （比如我上面的StudentDao，持久层）

*   结束

![](https://www.zby123.club/wp-content/uploads/2020/04/easyExcel3.png)

测试

```java
@SpringBootTest
class LombookApplicationTests {

    @Test
    void contextLoads() throws FileNotFoundException {

        //数据库 -> 数据持久化
        StudentDao studentdao = new StudentDaoImpl();

        ExcelReaderBuilder read =
                EasyExcel.read(
                        new FileInputStream("E:\\studentTest.xlsx"),
                        Student.class,
                        new ListenerExcel(studentdao));
        read.doReadAll();
    }
}
```

*   Result

![](https://www.zby123.club/wp-content/uploads/2020/04/easyExcel4-1024x284.png)

*   QA

1、 `@ExcelProperty`注解是用来指定每个字段的**列名称**，以及Excel中的**下标位置**

![](https://www.zby123.club/wp-content/uploads/2020/04/easyExcel5-1024x576.png)

这个地方我们只需要统一使用value或者index来指定excel中所对用的列即可，官方文档中不推荐同时使用value和index

2、`@DateTimeFormat("yyyy-MM-dd HH:mm:ss")`注解可以将时间格式化，但是需要时间类型为String

3、`@ExcelIgnore` 注解可以忽略掉当前属性，也就是excel中当前列

4、 `@ExcelProperty` 中的converter属性表示类型转换器（Excel转对象的数据处理，和对象集合转Excel的数据处理），根据Converter接口找到一些已经提供的的转换器

![](https://www.zby123.club/wp-content/uploads/2020/04/easyExcel7-1024x576.png)

当然我们可以重写这个接口实现，使用 converter 绑定到属性上

例如：

![](https://www.zby123.club/wp-content/uploads/2020/04/easyExcel8-1024x590.png)

重写`convertToJavaData`和`convertToExcelData`方法

代码：

```java
public class CustomStringStringConverter implements Converter<String> {
    @Override
    public Class supportJavaTypeKey() {
        return String.class;
    }

    @Override
    public CellDataTypeEnum supportExcelTypeKey() {
        return CellDataTypeEnum.STRING;
    }

    @Override
    public String convertToJavaData(CellData cellData, ExcelContentProperty contentProperty, GlobalConfiguration globalConfiguration) throws Exception {
        return "【学科专业】" + cellData.getStringValue();
    }

    @Override
    public CellData convertToExcelData(String value, ExcelContentProperty contentProperty, GlobalConfiguration globalConfiguration) throws Exception {
        return new CellData("【学科专业】" + value);
    }
}
```

在解析Excel文件的时候自动对对应的属性值进行转换

![](https://www.zby123.club/wp-content/uploads/2020/04/easyExcel9-1024x545.png)

同样，在生成Excel时也可以通过 `convertToExcelData` 方法自动转换数据

### 写(write)

*   先写一个生成测试的模板数据类

![](https://www.zby123.club/wp-content/uploads/2020/04/easyExcel10-1024x540.png)

代码如下：

```java
/**
 * @Author: 赵博雅
 * @Date: 2020/4/27 11:05
 */
public class DataTemplateImpl implements DataTemplate {
    @Override
    public List<Student> create() {

        List<Student> students = new ArrayList<>();

        Student build = Student.builder()
                .name("张三")
                .age(22)
                .gender("man")
                .height("170")
                .weight("120")
                .major("计算机科学与技术")
                .update("2020-04-27 11:17:50")
                .build();

        students.add(build);
        return students;
    }
}
```

*   生成Excel

![](https://www.zby123.club/wp-content/uploads/2020/04/easyExcel11-1024x486.png)

```java
@SpringBootTest
public class WriteTest {

    @Test
    void contextLoads() throws FileNotFoundException {

        String fileName = "E:\\" + "simpleWrite" + System.currentTimeMillis() + ".xlsx";
        //生成测试数据的模板
        DataTemplate dataTemplate = new DataTemplateImpl();
        EasyExcel.write(fileName, Student.class)
                .sheet("模板")
                .doWrite(dataTemplate.create());
    }
}
```

1、doWrite中传入数据

2、`excelType`可指定类型，使用`ExcelTypeEnum`的枚举类

![](https://www.zby123.club/wp-content/uploads/2020/04/easyExcel12.png)

*   生成Excel（自定义列）

1、使用`excludeColumnFiledNames`或者`excludeColumnIndexes`会根据列名或者列编码来屏蔽该列

![](https://www.zby123.club/wp-content/uploads/2020/04/easyExcel13-1024x506.png)

2、使用注解`@ExcelIgnore`同样可以忽略列

![](https://www.zby123.club/wp-content/uploads/2020/04/easyExcel14-1024x455.png)

体重 属性使用 `@ExcelIgnore` 忽略  
年龄 属性使用方法 `excludeColumnFiledNames` 忽略

*   复杂头写入

![](https://www.zby123.club/wp-content/uploads/2020/04/easyExcel16-1024x482.png)

#### web中的读和写（Swagger）

![](https://www.zby123.club/wp-content/uploads/2020/04/easyExcel19-1024x362.png)

*   读

![](https://www.zby123.club/wp-content/uploads/2020/04/easyExcel17-1024x227.png)

![](https://www.zby123.club/wp-content/uploads/2020/04/easyExcel20-1024x598.png)

*   写

![](https://www.zby123.club/wp-content/uploads/2020/04/easyExcel18-1024x552.png)

![](https://www.zby123.club/wp-content/uploads/2020/04/easyExcel21-1024x574.png)

下载模板