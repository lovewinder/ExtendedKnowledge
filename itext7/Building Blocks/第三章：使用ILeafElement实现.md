# 使用ILeafElement实现

  ElementPropertyContainer有三个直接的子类：Style，RootElement和AbstractElement。第一章中讨论了Style，之前的几章中也讨论了RootElement的子类Canvas和Document。在接下来的三章中来讨论AbstractElement类。
  
  * 这一章中会出现ILeafElement的一些实现--Tab,Link,Text和Image。
  * 下一章中会出现BlockElement对象Div,LineSeparator,List,ListItem和Paragraph。
  * 第五章中会出现BlockElement对象Table和Cell。

![A simple CSV file that will be used as data source](https://developers.itextpdf.com/sites/default/files/C03F01_0.png)
```
    public static final List<List<String>> convert(String src, String separator)
        throws IOException {
        List<List<String>> resultSet = new ArrayList<List<String>>();
        BufferedReader br = new BufferedReader(
            new InputStreamReader(new FileInputStream(src), "UTF8"));
        String line;
        List record;
        while ((line = br.readLine()) != null) {
            StringTokenizer tokenizer = new StringTokenizer(line, separator);
            record = new ArrayList<String>();
            while (tokenizer.hasMoreTokens()) {
                record.add(tokenizer.nextToken());
            }
            resultSet.add(record);
        }
        return resultSet;
    }
```
* 数据被存储在了List<List<String>>中（和我目前在做的数据处理一样麻烦）
* 准备将这个结构的数据添加到Tab中  
  
## Tab元素
![default tab positions](https://developers.itextpdf.com/sites/default/files/C02F02_1.png)
```
    List<List<String>> resultSet = CsvTo2DList.convert(SRC, "|");
    for (List<String> record : resultSet) {
        Paragraph p = new Paragraph();
        p.add(record.get(0).trim()).add(new Tab())
            .add(record.get(1).trim()).add(new Tab())
            .add(record.get(2).trim()).add(new Tab())
            .add(record.get(3).trim()).add(new Tab())
            .add(record.get(4).trim()).add(new Tab())
            .add(record.get(5).trim());
        document.add(p);
    }
```
* CsvTo2DList是自定义工具类
>>>
```
    PdfCanvas pdfCanvas = new PdfCanvas(pdf.addNewPage());
    for (int i = 1; i <= 10; i++) {
        pdfCanvas.moveTo(document.getLeftMargin() + i * 50, 0);
        pdfCanvas.lineTo(document.getLeftMargin() + i * 50, 595);
    }
    pdfCanvas.stroke();
```
* 添加辅助线，显示tab的位置

![different tab stop alignments](https://developers.itextpdf.com/sites/default/files/C03F04.png)
```
    float[] stops = new float[]{80, 120, 580, 590, 720};
    List<TabStop> tabstops = new ArrayList();
    tabstops.add(new TabStop(stops[0], TabAlignment.CENTER));
    tabstops.add(new TabStop(stops[1], TabAlignment.LEFT));
    tabstops.add(new TabStop(stops[2], TabAlignment.RIGHT));
    tabstops.add(new TabStop(stops[3], TabAlignment.LEFT));
    TabStop anchor = new TabStop(stops[4], TabAlignment.ANCHOR);
    anchor.setTabAnchor(' ');
    tabstops.add(anchor);
```
>>>
```
    List<List<String>> resultSet = CsvTo2DList.convert(SRC, "|");
    for (List<String> record : resultSet) {
        Paragraph p = new Paragraph();
        p.addTabStops(tabstops);
        p.add(record.get(0).trim()).add(new Tab())
            .add(record.get(1).trim()).add(new Tab())
            .add(record.get(2).trim()).add(new Tab())
            .add(record.get(3).trim()).add(new Tab())
            .add(record.get(4).trim()).add(new Tab())
            .add(record.get(5).trim() + " \'");
        document.add(p);
    }
```

![tab stops with tab leaders](https://developers.itextpdf.com/sites/default/files/C03F05_0.png)
```
    float[] stops = new float[]{80, 120, 580, 590, 720};
    List<TabStop> tabstops = new ArrayList();
    tabstops.add(new TabStop(stops[0], TabAlignment.CENTER, new DottedLine()));
    tabstops.add(new TabStop(stops[1], TabAlignment.LEFT));
    tabstops.add(new TabStop(stops[2], TabAlignment.RIGHT, new SolidLine(0.5f)));
    tabstops.add(new TabStop(stops[3], TabAlignment.LEFT));
    TabStop anchor = new TabStop(stops[4], TabAlignment.ANCHOR, new DashedLine());
    anchor.setTabAnchor(' ');
    tabstops.add(anchor);
```
***可以通过实现ILineDrawer接口来实现任何种类的线，itext提供了三种实现：SolidLine,DottedLine和DashedLine。每种实现都可以设置线宽和颜色，DottedLine类还允许你改变点之间的间隔。***

## Tab功能的限制
![using portrait orientation](https://developers.itextpdf.com/sites/default/files/C03F06_0.png)
***位置不够了，没有办法，作为7.0.0中的一个bug，将会在7.0.1中修复。***

## 添加links
![introducing links to IMDB](https://developers.itextpdf.com/sites/default/files/C03F08.png)
```
    List<List<String>> resultSet = CsvTo2DList.convert(SRC, "|");
    for (List<String> record : resultSet) {
        Paragraph p = new Paragraph();
        p.addTabStops(tabstops);
        PdfAction uri = PdfAction.createURI(
            String.format("http://www.imdb.com/title/tt%s", record.get(0)));
        Link link = new Link(record.get(2).trim(), uri);
        p.add(record.get(1).trim()).add(new Tab())
            .add(link).add(new Tab())
            .add(record.get(3).trim()).add(new Tab())
            .add(record.get(4).trim()).add(new Tab())
            .add(record.get(5).trim() + " \'");
        document.add(p);
    }
```

## Text类中的额外不常用的方法们
![extra text methods](https://developers.itextpdf.com/sites/default/files/C03F09.png)
```
    Text t1 = new Text("The Strange Case of ");
    Text t2 = new Text("Dr. Jekyll").setTextRise(5);
    Text t3 = new Text(" and ").setHorizontalScaling(2);
    Text t4 = new Text("Mr. Hyde").setSkew(10, 45);
    document.add(new Paragraph(t1).add(t2).add(t3).add(t4));
```
* setTextRise()：使文字上浮或下浮（参数为负）
* setHorizontalScaling()：使文字水平拉伸
* setSkew()：使文字倾斜，第一个参数是文字和baseline之间的角度，第二个参数是字体倾斜的角度

## 引入Images
![an image added to a document](https://developers.itextpdf.com/sites/default/files/C03F10.png)
```
    public static final String MARY = "src/main/resources/img/0117002.jpg";
    public void createPdf(String dest) throws IOException {
        PdfDocument pdf = new PdfDocument(
            new PdfWriter(new FileOutputStream(dest)));
        Document document = new Document(pdf);
        Paragraph p = new Paragraph(
            "Mary Reilly is a maid in the household of Dr. Jekyll: ");
        document.add(p);
        Image img = new Image(ImageDataFactory.create(MARY));
        document.add(img);
        document.close();
    }
```
***这里添加的是JPEG格式的图片，可以直接添加到document对象中，因为JPEG格式的图片可以在PDF中以原有的格式存储，如果是别的格式的图像，比如PNG，就需要做一些转换了。***

**Images是储存在content stream之外的对象，这个对象叫做image XObject**

![adding the same figure twice](https://developers.itextpdf.com/sites/default/files/C03F11_0.png)
  
  同样相同的内容，它们所占的空间却不同。
  1. V2
```
    Image img = new Image(ImageDataFactory.create(MARY));
    document.add(img);
    document.add(img);
```
  2. V3
```
    Image img1 = new Image(ImageDataFactory.create(MARY));
    document.add(img1);
    Image img2 = new Image(ImageDataFactory.create(MARY));
    document.add(img2);
```
  ***因此尽量避免为同一个图片创建不同的实例***
  
## 调整图片的位置和宽度
![adding an image at absolute positions](https://developers.itextpdf.com/sites/default/files/C03F12.png)
**V4**
```
      Image img = new Image(ImageDataFactory.create(MARY), 320, 750, 50);
      document.add(img);
```    
>>>
**V5**
```
    Image img = new Image(ImageDataFactory.create(MARY));
    img.setFixedPosition(320, 750, UnitValue.createPointValue(50));
    document.add(img);
```

* 图片的位置可以在Image对象的构造方法中设置，也可以使用setFixedPosition()方法设置。
* 用以上方法设置图片宽度时，图片的高度也会同时同比例变化。

![adding an image on a specific page](https://developers.itextpdf.com/sites/default/files/C03F13.png)
* setFixedPosition()方法中可以有个参数来设置图片具体所在的页码。

## 向已完成的PDF中添加图片
***在itext5中，使用PdfStamper对象向已存在的PDF中添加内容，在itext7中该对象不再存在。***
![adding an image to an existing PDF](https://developers.itextpdf.com/sites/default/files/C03F14.png)
```
    public void manipulatePdf(String src, String dest) throws IOException {
        PdfReader reader = new PdfReader(src);
        PdfWriter writer = new PdfWriter(dest);
        PdfDocument pdfDoc = new PdfDocument(reader, writer);
        Document document = new Document(pdfDoc);
        Image img = new Image(ImageDataFactory.create(MARY));
        img.setFixedPosition(1, 350, 750, UnitValue.createPointValue(50));
        document.add(img);
        document.close();
    }
```

## 改变图片的大小和旋转图片

![defining the width as a percentage](https://developers.itextpdf.com/sites/default/files/C03F15.png)
```
    Image img = new Image(ImageDataFactory.create(MARY));
    img.setHorizontalAlignment(HorizontalAlignment.CENTER);
    img.setWidthPercent(80);
    document.add(img);
```

***itext不会改变图片的质量，但不意味着分辨率不会被改变***

![adding an image to a Paragraph](https://developers.itextpdf.com/sites/default/files/C03F16.png)
```
    Paragraph p = new Paragraph(
        "Mary Reilly is a maid in the household of Dr. Jekyll: ");
    Image img = new Image(ImageDataFactory.create(MARY));
    p.add(img);
    document.add(p);
```


![scaled and rotated image](https://developers.itextpdf.com/sites/default/files/C03F17.png)
```
    Paragraph p = new Paragraph(
        "Mary Reilly is a maid in the household of Dr. Jekyll: ");
    Image img = new Image(ImageDataFactory.create(MARY));
    img.scale(0.5f, 0.5f);
    img.setRotationAngle(-Math.PI / 6);
    p.add(img);
    document.add(p);
```

* scale()：第一个参数--宽度比例；第二个参数--高度比例
* scaleAbsolute()：第一个参数--具体宽度；第二个参数--具体高度
* scaleToFit()：第一个参数--最大宽度；第二个参数--最大高度（会保证宽高比例，不会让图片出现奇奇怪怪的规格）

## itext支持的图片类型
* JPEG
* JPEG2000
* BMP
* PNG
* GIF
* JBIG2
* TIFF
* WMF

### 加工图片数据
![raw image](https://developers.itextpdf.com/sites/default/files/C03F18.png)
```
    byte data[] = new byte[256 * 3];
    for (int i = 0; i < 256; i++) {
        data[i * 3] = (byte) (255 - i);
        data[i * 3 + 1] = (byte) (255 - i);
        data[i * 3 + 2] = (byte) i;
    }
    ImageData raw = ImageDataFactory.create(256, 1, 3, 8, data, null);
    Image img = new Image(raw);
    img.scaleAbsolute(256, 10);
    document.add(img);
```
* 可用颜色分为三种：256；RGB；CMYK

**然后下边介绍了所有类型的图片的添加过程和一些处理方法，暂时用不着**
