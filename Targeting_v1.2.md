## v1.2

1. 新增标签<merge_order>

标签位于template_style.xml文件中的<tab ...>标签下，样式如下：
```
  <merge_order>2~1~3~4~5</merge_order>
```
这个标签表名表的合并顺序为2,1,3,4,5.

**这个标签是必要标签，当表格类型为2时，即有<table_content_layout>和<table_header_layout>标签时，这个标签页必须有。**

2. 新增实体类UtilCell
```
@Data
public class UtilCell {
    // 对应原来的 s1.split("#")[k]
    private String con;
    // 对应原来的 （ct-row+1)+"/1"或"1/1"
    private String mer;
    // 对应原来的 cells[k]
    private CusCell cusCell;
    // 对应原来的 table
    private PdfPTable table;
    // 对应原来的 fonts[k]
    private Font font;

    // UtilCell编号
    private double serial_num;

    public UtilCell(String con, String mer, CusCell cusCell, PdfPTable table, Font font,
            double serial_num) {
        this.con = con;
        this.mer = mer;
        this.cusCell = cusCell;
        this.table = table;
        this.font = font;
        this.serial_num = serial_num;
        // System.out.println(serial_num);
    }

    public UtilCell(String con, String mer, CusCell cusCell, PdfPTable table, Font font) {
        this.con = con;
        this.mer = mer;
        this.cusCell = cusCell;
        this.table = table;
        this.font = font;
        // this.serial_num = serial_num;
    }
}
```

3. UtilCell类的用法

修改merTable(...)方法，现在这个方法将会返回一个Map<Double,UtilCell>对象

再调用SortData.sortTableMap(Map<Double,UtilCell>)方法，可以返回一个List<UtilCell>的列表

这个列表按实际添加顺序存储组成一个表格所需的所有的UtilCell对象，遍历这个对象，并使用对应的get方法，就能取到所需要的参数

```
Map<Double,UtilCell> map = TabModel.merTable(...);

List<UtilCell> uclist = SortData.sortTableMap(map);

for(UtilCell uc : uclist){
  String con = uc.getCon();
  ...
  ...
}
```

如果有需要额外加进去的属性，可以直接反馈给我，这样你那边就不再需要跟着我的结构改了，之前那种方式耦合太严重了

4. merTable()方法新增参数

```
merTable(...,int col,double[] order){
    ...
}
```

col表示表格列数，order数组是<merge_order>...</merge_order>标签中的内容

