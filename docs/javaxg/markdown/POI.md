# 1.POI简介

## 1.1什么是poi

Apache POI 是用Java编写的免费开源的跨平台的 Java API，Apache POI提供API给Java对Microsoft Office格式档案读和写的功能。POI为“Poor Obfuscation Implementation”的首字母缩写，意为“简洁版的模糊实现”。



## 1.2poi常用的包

HSSF － 提供读写Microsoft Excel XLS格式档案的功能。

XSSF － 提供读写Microsoft Excel OOXML XLSX格式档案的功能。

HWPF － 提供读写Microsoft Word DOC格式档案的功能。

HSLF － 提供读写Microsoft PowerPoint格式档案的功能。

HDGF － 提供读Microsoft Visio格式档案的功能。

HPBF  － 提供读Microsoft Publisher格式档案的功能。

HSMF － 提供读Microsoft Outlook格式档案的功能。

# 2.poi简单使用

## 2.1新建一个maven项目

项目名：compoidemo

## 2.2导入依赖

```XML
<dependencies>
    <!--xls-->
    <dependency>
        <groupId>org.apache.poi</groupId>
        <artifactId>poi</artifactId>
        <version>3.9</version>
    </dependency>

    <!--xlsx-->
    <dependency>
        <groupId>org.apache.poi</groupId>
        <artifactId>poi-ooxml</artifactId>
        <version>3.9</version>
    </dependency>


    <!--test-->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
    </dependency>
</dependencies>
```

## 2.3POI操作Excel的使用

（1）POI用来操作office读写的功能的工具

（2）2003版本的（.xls）HSSFWorkbook 和2007版本的（.xlsx）XSSFWorkbook

（3）结构：workbook--sheet--row--cell

（4）步骤：

```
//1.创建workbook
//2.根据workbook创建sheet
//3.根据sheet创建row
//4.根据row创建cell
//5.向cell里面设置值
//6.通过输出流写到文件里去
（5）Excel写操作
```

```JAVA
@Test
    public void writeExcel03() throws Exception {
        //1.创建workbook
        Workbook workbook=new HSSFWorkbook();
        //2.根据workbook创建sheet
        Sheet sheet = workbook.createSheet("会员列表");
        //3.根据sheet创建row
        Row row1 = sheet.createRow(0);
        //4.根据row创建cell
        Cell cell1 = row1.createCell(0);
        //5.向cell里面设置值
        cell1.setCellValue("按键");
        //6.通过输出流写到文件里去
        FileOutputStream fos=new FileOutputStream("D:\\123\\01.xls");
        workbook.write(fos);
        fos.close();
    }
```

（6）Excel读操作

```JAVA
@Test
    public void readExcel03() throws Exception {
        FileInputStream fis=new FileInputStream("D:\\123\\01.xls");
        Workbook workbook=new HSSFWorkbook(fis);
        Sheet sheet = workbook.getSheetAt(0);
        Row row = sheet.getRow(0);
        Cell cell = row.getCell(0);
        System.out.println(cell.getStringCellValue());
    }
```

## 2.4POI操作word的使用

（1）安装依赖

```XML
<dependency>
    <groupId>org.apache.poi</groupId>
    <artifactId>poi-scratchpad</artifactId>
    <version>3.8</version>
</dependency>
```

（2）word读操作

```java
    @Test
    public void readWord() throws Exception {
        //得到.docx文件提取器
        XWPFWordExtractor docx = new XWPFWordExtractor(POIXMLDocument.openPackage("C:\\Users\\eric.fang\\Desktop\\demo.docx"));
        //提取.docx正文文本   
        String text = docx.getText();
        System.out.println(text);
}
```

https://www.cnblogs.com/fqh2020/p/14675105.html

