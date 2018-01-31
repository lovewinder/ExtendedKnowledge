# 介绍基本模块

## 创建文档
```
    PdfWriter writer = new PdfWriter(dest);
    PdfDocument pdf = new PdfDocument(writer);
    Document document = new Document(pdf);
    document.add(new Paragraph("Hello World!"));
    document.close();
```
* 创建**PdfWriter**，它可以创建一个pdf文件，它不知道文档和什么有关，它仅仅是为了保证文档的结构完整。在这个实例中，向构造方法中传入了一个String作为参数，参数代表着文档的路径，比如：*result/chapter01/hello_world.pdf*

    这个构造方法同时可以接收*OutputStream*作为参数。
    
    * 如果要写一个web应用程序,参数可以为*ServletOutputStream*
    
    * 如果要写到内存中,参数可以为*ByteArrayOutputStream*
      
* **PdfWriter**知道什么被写进了文档,因为它监听了**PdfDocument**
    **PdfDocument**管理被添加的内容,将内容分配到不同的页面,并且跟踪任何与该内容有关的信息
    
* 一旦我们创建了**PdfWriter**和**PdfDocument**,我们就已经完成了低级模块
    
    接下来创建**Document**对象,将**PdfDocument**作为参数
    
* 创建包含"Hello World"的**Paragraph**,然后将paragraph添加到**document**中   

* 最后关闭document,就完成了PDF的创建

## 添加复杂段落
```
PdfFont font = PdfFontFactory.createFont(FontConstants.TIMES_ROMAN);
// Add a Paragraph
document.add(new Paragraph("iText is:").setFont(font));
// Create a List
List list = new List()
    .setSymbolIndent(12)
    .setListSymbol("\u2022")
    .setFont(font);
// Add ListItem objects
list.add(new ListItem("Never gonna give you up"))
    .add(new ListItem("Never gonna let you down"))
    .add(new ListItem("Never gonna run around and desert you"))
    .add(new ListItem("Never gonna make you cry"))
    .add(new ListItem("Never gonna say goodbye"))
    .add(new ListItem("Never gonna tell a lie and hurt you"));
// Add the list
document.add(list);
```
* iText使用Helvetica作为默认字体。可以通过创建PdfFont实例来修改字体，可以通过PdfFontFactory获取PdfFont实例。
* 可以以List形式添加段落，list可以设置缩进，设置字体。通过add(new ListItem())向list添加内容。

## 向段落中添加图片
```
    Image fox = new Image(ImageDataFactory.create(FOX));
    Image dog = new Image(ImageDataFactory.create(DOG));
    Paragraph p = new Paragraph("The quick brown ")
                .add(fox)
                .add(" jumps over the lazy ")
                .add(dog);
    document.add(p);
```
* 向ImageDataFactory中传递一个图片路径，这个工厂类会帮你根据传入图片的类型（jpg,png,gif,bmp...)来帮你把它们处理成在PDF中可用的图片。
* 将得到的Image实例添加到段落中即可。

# 发布数据库
```
    PdfWriter writer = new PdfWriter(dest);
    PdfDocument pdf = new PdfDocument(writer);    
    Document document = new Document(pdf, PageSize.A4.rotate());
    document.setMargins(20, 20, 20, 20);
    PdfFont font = PdfFontFactory.createFont(FontConstants.HELVETICA);
    PdfFont bold = PdfFontFactory.createFont(FontConstants.HELVETICA_BOLD);
    Table table = new Table(new float[]{4, 1, 3, 4, 3, 3, 3, 3, 1});
    table.setWidthPercent(100);
    BufferedReader br = new BufferedReader(new FileReader(DATA));
    String line = br.readLine();
    process(table, line, bold, true);
    while ((line = br.readLine()) != null) {
        process(table, line, font, false);
    }
    br.close();
    document.add(table);
    document.close();
```
* 默认的页面大小为A4，PageSize.A4.rotate()表示使用横向页面
* 默认的页边距为36像素，setMargins(20,20,20,20)可以设置页边距（）
* Table对象，上边代码中的创建表格对象的方式中，表示一共创建9列，数字代表了每一列的相对宽度。表格的总宽度为页面宽度的100%减去页边距。
* 读取第一行作为表头，设置表头字体为bold。读取剩下的内容，作为普通表格。
```
public void process(Table table, String line, PdfFont font, boolean isHeader) {
    StringTokenizer tokenizer = new StringTokenizer(line, ";");
    while (tokenizer.hasMoreTokens()) {
        if (isHeader) {
            table.addHeaderCell(
                new Cell().add(
                    new Paragraph(tokenizer.nextToken()).setFont(font)));
        } else {
            table.addCell(
                new Cell().add(
                    new Paragraph(tokenizer.nextToken()).setFont(font)));
        }
    }
}
```
* table：被添加的表格
* line：按行读取的文本内容
* font：字体
* isHeader：是否为表头

* table.addHeaderCell(cell)：添加表头单元格
* table.addCell(cell)：添加普通单元格

# 添加低级内容

```
canvas.moveTo(-406,0)
      .lineTo(406,0)
      .stroke();
```

## 用canvas画线
```
PdfDocument pdf = new PdfDocument(new PdfWriter(dest));
PageSize ps = PageSize.A4.rotate();
PdfPage Page = pdf.addNewPage(ps);
PdfCanvas canvas = new PdfCanvas(page);
// Draw the axes
pdf.close();
```
* 首先不再使用Document对象
* 创建PdfPage实例，并创建PdfCanvas实例
* 使用canvas对象绘制图形
* 关闭PdfDocument

***在上一章中最后关闭文档是通过document.close()来完成的，现在没有Document对象，不得不通过pdf.close()关闭文档***

**在PDF中，72个元素是一英尺。**

## 坐标系统和转换矩阵
```
canvas.concatMatrix(1,0,0,1,ps.getWidth()/2,ps.getHeight()/2)
```
* 可以通过转化矩阵来移动对象的位置
* 在PDF中，不会去移动对象，而是去移动坐标系统并在新的坐标系统中绘制对象
* 绘制的源坐标系统的位置在页面的中心

```
a  b  0
c  d  0
e  f  1
```
* 第三列永远是“0,0,1”，因为是二维空间
* a代表缩放，b代表旋转，c代表坐标轴的斜度
* e和f定义了坐标的平移位置

## 图形的状态
### 绘制坐标系
```
// Draw X axis
canvas.moveTo(-(ps.getWidth()/2-15),0)
      .lineTo(ps.getWidth()/2-15),0)
      .stroke();
// Draw X axis arrow
canvas.setLineJoinStyle(PdfCanvasConstants.LineJoinStyle.ROUND)
      .moveTo(ps.getWidth()/2-25,-10)
      .lineTo(ps.getWidth()/2-15,0)
      .lineTo(ps.getWidth()/2-25,10).stroke();
      .setLineJoinStyle(PdfCanvasConstants.LineJoinStyle.MITER);
// Draw Y axis
canvas.moveTo(0,-(ps.getHeight()/2-15))
      .lineTo(0,ps.getHeight()/2-15)
      .stroke();
// Draw Y axis arrow
canvas.saveState()
      .setLineJoinStyle(PdfCanvasConstants.LineJoinStyle.ROUND)
      .moveTo(-10,ps.getHeight()/2-25)
      .lineTo(0,ps.getHeight()/2-15)
      .lineTo(10,ps.getHeight()/2-25).stroke()
      .restoreState();
// Draw X serif
for(int i = -((int)ps.getWidth()/2-61);
    i<((int)ps.getWidth()/2-60;
    i+=40){
            canvas.moveTo(i,5).lineTo(i,-5);
       }
// Draw Y serif
for(int = -(int)ps.getHeight()/2-57);
    i<((int)ps.getWidth()/2-56;
    j+=40){
            canvas.moveTo(5,j).lineTo(-5,j);
         }
canvas.stroke();
```
* 横坐标最左端为负，说明了concatMatrix()方法是可以将实际的坐标原点做修改并应用的
* 接下来绘制了两个连接的线：
    1. miter-尖角连接（默认）
    2. bevel-斜面角连接
    3. round-圆角连接
* 之后使用setLineJoinStyle(...)恢复canvas的原始状态，但这并不是最佳的方法
* 在更改canvas状态之前，使用saveState()，接下来就可以使用这种状态的canvas绘制各种图形了，最后使用restoreState()将canvas的状态恢复到saveState()之前的状态
* stroke()方法可以在所有图形绘制完成之后再使用

>>> 有些特殊的约定需要遵守saveState()和restoreState()要成对出现

### 颜色
```
Color grayColor = new DeviceCmyk(0.f,0.f,0.f,0.875f);
Color greenColor = new DeviceCmyk(1.f,0.f,1.f,0.176f);
Color blueColor = new DeviceCmyk(1.f,0.156f,0.f,0.118f);
```
* PDF中有许多不同的颜色空间。在itext中，每一种都在不同的class中被实现，最常用的有：
  1. DeviceGray-只用一个参数确定
  2. DeviceRgb-由三个参数确定（红，绿，蓝）
  3. DeviceCmyk-由四个参数确定（青，红，黄，黑）

**注意使用Color所引用的包是com.itextpdf.kernel.color而不是java.awt.Color**

### 绘制网格
```
canvas.setLineWidth(0.5f).setStrokeColor(blueColor);
for ( int i = -((int) ps.getHeight() / 2 - 57);
      i < ((int) ps.getHeight() / 2 -56);
      i += 40){
            canvas.moveTo(-(ps.getWidth() / 2 - 15),i)
                  .lineTo(ps.getWidth() / 2 - 15,i);
      }
for ( int j = -((int) ps.getWidth() / 2 - 61);
      j < ((int) ps.getWidth() / 2 -60);
      j += 40){
            canvas.moveTo(j,-(ps.getHeight() / 2 - 15))
                  .lineTo(j,ps.getHeight() / 2 -15）；
      }
}
canvas.stroke();
```
### 绘制虚线
```
canvas.setLineWidth(2).setStrokeColor(greenColor)
      .setLineDash(10,10,8)
      .moveTo(-(ps.getWidth() / 2 - 15),-(ps.getHeight() / 2 -15))
      .lineTo(ps.getWidth() / 2 - 15,ps.getHeight() / 2 -15).stroke();
```
* 有多种绘制虚线的方式，上述的方式是使用了3个参数的方式
  1. 第一个参数-实线长度
  2. 间隙长度
  3. 起始距离（体现在第一个虚线段上，没有完全理解，可配合修改代码参数观察生成的pdf理解）
  
**还有许多其他的常用绘制方法**
1. curveTo()
2. rectangle()
3. fill()

## 文本的状态
![在绝对位置添加文本](https://developers.itextpdf.com/sites/default/files/C02F03.png)
```
    List<String> text = new ArrayList();
    text.add("         Episode V         ");
    text.add("  THE EMPIRE STRIKES BACK  ");
    text.add("It is a dark time for the");
    text.add("Rebellion. Although the Death");
    text.add("Star has been destroyed,");
    text.add("Imperial troops have driven the");
    text.add("Rebel forces from their hidden");
    text.add("base and pursued them across");
    text.add("the galaxy.");
    text.add("Evading the dreaded Imperial");
    text.add("Starfleet, a group of freedom");
    text.add("fighters led by Luke Skywalker");
    text.add("has established a new secret");
    text.add("base on the remote ice world");
    text.add("of Hoth...");
```
* 实现上图PDF的最好方法是使用一个Paragraph对象序列with不同的对齐方式（center for title,left for body），之后将这些Paragraph对象添加到Document中（high-level方法）
* 如果是high-level方法，添加过程中会自动换行，会自动换页
* 如果是low-level方法，需要自己手动去把文本分割成小的chunk

```
canvas.concatMartx(1,0,0,1,0,ps.getHeight());
canvas.beginText()
      .setFontAndSize(PdfFontFactory.createFont(FontConstants.COURIER_BOLD),14)
      .setLeading(14 * 1.2f)
      .moveText(70,-40);
```
* 将坐标系统的原点设在左上角
* beginText()创建text对象
* setFontAndSize()设置字体和字号，作用于所有的canvas所添加的text对象的字体和字号
* setLeading()设置行距为字号的1.2倍
* moveText()设置光标起始位置
```
for(String s:text){
      // Add text and move to the next line
      canvas.newlineShowText(s);
}
canvas.endText();
```
* newlineShowText()会将光标移动到之前一行的下边的16.2个单位（行距）处
* endText()关闭text对象

![adding skewed and colored text at absolute positions](https://developers.itextpdf.com/sites/default/files/C02F04.png)

```
canvas.rectangel(0,0,ps.getWidth(),ps.getHeight())
      .setColor(Color.BLACK,true)
      .fill();
```
* 可以使用setFillColor(Color.BLACK)来为矩形填充颜色，但选择了更为通用的setColor()方法，true or false决定了是为fill还是为stroke上色

```
    canvas.concatMatrix(1, 0, 0, 1, 0, ps.getHeight());
    Color yellowColor = new DeviceCmyk(0.f, 0.0537f, 0.769f, 0.051f);
    float lineHeight = 5;
    float yOffset = -40;
    canvas.beginText()
        .setFontAndSize(PdfFontFactory.createFont(FontConstants.COURIER_BOLD), 1)
        .setColor(yellowColor, true);
    for (int j = 0; j < text.size(); j++) {
        String line = text.get(j);
        float xOffset = ps.getWidth() / 2 - 45 - 8 * j;
        float fontSizeCoeff = 6 + j;
        float lineSpacing = (lineHeight + j) * j / 1.5f;
        int stringWidth = line.length();
        for (int i = 0; i < stringWidth; i++) {
            float angle = (maxStringWidth / 2 - i) / 2f;
            float charXOffset = (4 + (float) j / 2) * i;
            canvas.setTextMatrix(fontSizeCoeff, 0,
                    angle, fontSizeCoeff / 1.5f,
                    xOffset + charXOffset, yOffset - lineSpacing)
                .showText(String.valueOf(line.charAt(i)));
        }
    }
    canvas.endText();
```
* 因为不再使用newlineShowText()，所以不需要定义leading
* 设置文字的颜色时，使用的是setColor(yellowColor,true)。这是因为文字会占用路径中所有的内容，因此使用true
* canvas.setTextMatrix(fontSizeCoeff,0,angle,fontSizeCoeff/1.5f,xOffset+charXOffset,yOffset-lineSpacing).showText(String.valueOf(line.charAt(i)));a和d代表着缩放，c代表着倾斜度，e和f代表着位置

# 使用渲染器和事件处理器

## document渲染器
![Text and images organized in columns](https://developers.itextpdf.com/sites/default/files/C03F01.png)
```
    PdfDocument pdf = new PdfDocument(new PdfWriter(dest));
    PageSize ps = PageSize.A5;
    Document document = new Document(pdf, ps);
     
    //Set column parameters
    float offSet = 36;
    float columnWidth = (ps.getWidth() - offSet * 2 + 10) / 3;
    float columnHeight = ps.getHeight() - offSet * 2;
     
    //Define column areas
    Rectangle[] columns = {
        new Rectangle(offSet - 5, offSet, columnWidth, columnHeight),
        new Rectangle(offSet + columnWidth, offSet, columnWidth, columnHeight),
        new Rectangle(
            offSet + columnWidth * 2 + 5, offSet, columnWidth, columnHeight)};
    document.setRenderer(new ColumnDocumentRenderer(document, columns));
     
    // adding content
    Image inst = new Image(ImageDataFactory.create(INST_IMG)).setWidth(columnWidth);
    String articleInstagram = new String(
        Files.readAllBytes(Paths.get(INST_TXT)), StandardCharsets.UTF_8);
     
     // The method addArticle is defined in the full  NewYorkTimes sample
    NewYorkTimes.addArticle(document,
        "Instagram May Change Your Feed, Personalizing It With an Algorithm",
        "By MIKE ISAAC MARCH 15, 2016", inst, articleInstagram);
     
    document.close();
```
* offSet定义了页边距
* columnWidth定义了每一列的宽度（注意每一列之间是有间隔的，假设为5个单位），总宽度是页面宽度减去两倍的offSet加上两个5
* columnHeight定义了每一列的高度
* Rectangle对象，new Rectangle(a,b,c,d)
  * a表示矩形起始横坐标
  * b表示矩形起始纵坐标
  * c表示矩形宽度
  * d表示矩形高度
* setRenderer()，一旦声明了document的渲染器，接下来的内容布局都要符合这个定义

```
    public static void addArticle(
        Document doc, String title, String author, Image img, String text)
        throws IOException {
        Paragraph p1 = new Paragraph(title)
                .setFont(timesNewRomanBold)
                .setFontSize(14);
        doc.add(p1);
        doc.add(img);
        Paragraph p2 = new Paragraph()
                .setFont(timesNewRoman)
                .setFontSize(7)
                .setFontColor(Color.GRAY)
                .add(author);
        doc.add(p2);
        Paragraph p3 = new Paragraph()
                .setFont(timesNewRoman)
                .setFontSize(10)
                .add(text);
        doc.add(p3);
    }
```
* timesNewRoman = PdfFontFactory.createFont(FontConstants.TIMES_ROMAN);
  
  timesNewRomanBold = PdfFontFactory.createFont(FontConstants.TIMES_BOLD);
  
## 使用块渲染器

**默认情况下，表格单元格没有背景颜色，边框为黑色，边框线宽为0.5个单位**
![a table with colored cells and rounded borders](https://developers.itextpdf.com/sites/default/files/C03F02_0.png)
```
PageSize ps = new PageSize(842,680);
```
* 设置自定义页面大小
```
    PdfFont font = PdfFontFactory.createFont(FontConstants.HELVETICA);
    PdfFont bold = PdfFontFactory.createFont(FontConstants.HELVETICA_BOLD);
    Table table = new Table(new float[]{1.5f, 7, 2, 2, 2, 2, 3, 4, 4, 2});
    table.setWidthPercent(100)
            .setTextAlignment(TextAlignment.CENTER)
            .setHorizontalAlignment(HorizontalAlignment.CENTER);
    BufferedReader br = new BufferedReader(new FileReader(DATA));
    String line = br.readLine();
    process(table, line, bold, true);
    while ((line = br.readLine()) != null) {
        process(table, line, font, false);
    }
    br.close();
    document.add(table);
```
* 表格占据了可用页面100%的宽度（是否会留有页边距？）

```
    public void process(Table table, String line, PdfFont font, boolean isHeader) {
        StringTokenizer tokenizer = new StringTokenizer(line, ";");
        int columnNumber = 0;
        while (tokenizer.hasMoreTokens()) {
            if (isHeader) {
                Cell cell = new Cell().add(new Paragraph(tokenizer.nextToken()));
                cell.setNextRenderer(new RoundedCornersCellRenderer(cell));
                cell.setPadding(5).setBorder(null);
                table.addHeaderCell(cell);
            } else {
                columnNumber++;
                Cell cell = new Cell().add(new Paragraph(tokenizer.nextToken()));
                cell.setFont(font).setBorder(new SolidBorder(Color.BLACK, 0.5f));
                switch (columnNumber) {
                    case 4:
                        cell.setBackgroundColor(greenColor);
                        break;
                    case 5:
                        cell.setBackgroundColor(yellowColor);
                        break;
                    case 6:
                        cell.setBackgroundColor(redColor);
                        break;
                    default:
                        cell.setBackgroundColor(blueColor);
                        break;
                }
                table.addCell(cell);
            }
        }
    }
```
* setBorder()覆盖了默认的border

***SolidBorder类继承了Border类，同级的类还有DashedBorder，DottedBorder，DoubleBorder等等。如果iText并没有提供你想要的border种类，
你可以继承Border类，或者以已经提供的接口为灵感创建自定义的CellRenderer接口***

```
    private class RoundedCornersCellRenderer extends CellRenderer {
        public RoundedCornersCellRenderer(Cell modelElement) {
            super(modelElement);
        }
     
        @Override
        public void drawBorder(DrawContext drawContext) {
            Rectangle rectangle = getOccupiedAreaBBox();
            float llx = rectangle.getX() + 1;
            float lly = rectangle.getY() + 1;
            float urx = rectangle.getX() + getOccupiedAreaBBox().getWidth() - 1;
            float ury = rectangle.getY() + getOccupiedAreaBBox().getHeight() - 1;
            PdfCanvas canvas = drawContext.getCanvas();
            float r = 4;
            float b = 0.4477f;
            canvas.moveTo(llx, lly).lineTo(urx, lly).lineTo(urx, ury - r)
                    .curveTo(urx, ury - r * b, urx - r * b, ury, urx - r, ury)
                    .lineTo(llx + r, ury)
                    .curveTo(llx + r * b, ury, llx, ury - r * b, llx, ury - r)
                    .lineTo(llx, lly).stroke();
            super.drawBorder(drawContext);
        }
    }
```
* 这是一个自定义的CellRenderer，叫作RoundedCornersCellRenderer()
* setBorder(null)，如果不设置为空的话，会出现两个重叠的单元格，一个是itext绘制的，另一个是渲染器绘制的
* CellRenderer类是BlockRenderer类的特殊实现

***BlockRenderere类可以被用于BlockElements，比如Paragraph和List。这些渲染器允许你重写draw()来创建自定义的渲染器。***

* getOccupiedAreaBBox()返回一个Rectangle对象，对象内容为这个BlockElement所对应的边界框
* 参数drawContext可以提供PdfCanvas实例
* 这个例子演示了如何将high-level（Cell）和low-level方法绑定

***绘制曲线的时候需要一些数学知识，但是这并不复杂，别害怕***

## 事件处理器
![repeating background color and watermark](https://developers.itextpdf.com/sites/default/files/C03F03_1.png)
```
    PdfDocument pdf = new PdfDocument(new PdfWriter(dest));
    pdf.addEventHandler(PdfDocumentEvent.END_PAGE, new MyEventHandler());
    Document document = new Document(pdf);
    Paragraph p = new Paragraph("List of reported UFO sightings in 20th century")
            .setTextAlignment(Property.TextAlignment.CENTER)
            .setFont(helveticaBold).setFontSize(14);
    document.add(p);
    Table table = new Table(new float[]{3, 5, 7, 4});
    table.setWidthPercent(100);
    BufferedReader br = new BufferedReader(new FileReader(DATA));
    String line = br.readLine();
    process(table, line, helveticaBold, true);
    while ((line = br.readLine()) != null) {
        process(table, line, helvetica, false);
    }
    br.close();
    document.add(table);
    document.close();
```
* addEventHandler(PdfDocumentEvent.END_PAGE,new MyEventHandler());MyEventHandler是接口IEventHandler的实现，
这个接口中只有一个方法：handleEvent()。当页面结束时这个方法将会触发（页面结束包括新页面的创建和旧页面的结束）
```
    protected class MyEventHandler implements IEventHandler {
        public void handleEvent(Event event) {
            PdfDocumentEvent docEvent = (PdfDocumentEvent) event;
            PdfDocument pdfDoc = docEvent.getDocument();
            PdfPage page = docEvent.getPage();
            int pageNumber = pdfDoc.getPageNumber(page);
            Rectangle pageSize = page.getPageSize();
            PdfCanvas pdfCanvas = new PdfCanvas(
                page.newContentStreamBefore(), page.getResources(), pdfDoc);
     
            //Set background
            Color limeColor = new DeviceCmyk(0.208f, 0, 0.584f, 0);
            Color blueColor = new DeviceCmyk(0.445f, 0.0546f, 0, 0.0667f);
            pdfCanvas.saveState()
                    .setFillColor(pageNumber % 2 == 1 ? limeColor : blueColor)
                    .rectangle(pageSize.getLeft(), pageSize.getBottom(),
                        pageSize.getWidth(), pageSize.getHeight())
                    .fill().restoreState();
            //Add header and footer
            pdfCanvas.beginText()
                    .setFontAndSize(helvetica, 9)
                    .moveText(pageSize.getWidth() / 2 - 60, pageSize.getTop() - 20)
                    .showText("THE TRUTH IS OUT THERE")
                    .moveText(60, -pageSize.getTop() + 30)
                    .showText(String.valueOf(pageNumber))
                    .endText();
            //Add watermark
            Canvas canvas = new Canvas(pdfCanvas, pdfDoc, page.getPageSize());
            canvas.setFontColor(Color.WHITE);
            canvas.setProperty(Property.FONT_SIZE, 60);
            canvas.setProperty(Property.FONT, helveticaBold);
            canvas.showTextAligned(new Paragraph("CONFIDENTIAL"),
                298, 421, pdfDoc.getPageNumber(page),
                TextAlignment.CENTER, VerticalAlignment.MIDDLE, 45);
     
            pdfCanvas.release();
        }
    }
```

***不同的路径和形状在统一页面上可以重叠，绘制的规则就是，content stream先来的就先画。我们想要在添加背景时每次都能够将所有的
页面完全渲染。每个PdfPage对象都保存了一组content streams，你可以将索引作为参数，使用getContentStream()方法来获取每个单独的content stream。
可以使用getFirstContentStream()和getLastContentStream()来获取第一个和最后一个content stream。也可以使用newContentStreamBefore()和
newContentStreamAfter()方法创建新的content stream***

* 在handleEvent()方法中，我们使用了PdfCanvas的构造方法，下面介绍一下相关参数：
  * page.newContentStreamBefore()：为了不使页眉页脚和水印覆盖正文内容
  * page.getResources()：每个content stream都和一些外部资源相关，比如字体和图片，itext需要知道这些资源
  * pdfDoc

  
# 制作具有交互功能的PDF（当前本章内容我们很少涉及）

## 添加Annotations
![a text annotation](https://developers.itextpdf.com/sites/default/files/C04F01.png)
```
    PdfAnnotation ann = new PdfTextAnnotation(new Rectangle(20, 800, 0, 0))
        .setColor(Color.GREEN)
        .setTitle(new PdfString("iText"))
        .setContents("With iText, "
            + "you can truly take your documentation needs to the next level.")
        .setOpen(true);
    pdf.getFirstPage().addAnnotation(ann);
```
![a link annotation](https://developers.itextpdf.com/sites/default/files/C04F02.png)
```
    PdfLinkAnnotation annotation = new PdfLinkAnnotation(new Rectangle(0, 0))
            .setAction(PdfAction.createURI("http://itextpdf.com/"));
    Link link = new Link("here", annotation);
    Paragraph p = new Paragraph("The example of link annotation. Click ")
            .add(link.setUnderline())
            .add(" to learn more...");
    document.add(p);
```
***link ann不会被添加到content stream中，因为ann不属于content stream的一部分。它反而会被添加到对应页面的对应坐标系中（这句话的具体意义不明，暂时不用深究）。
文本被设置为可点击的，并不会改变文本在content stream中的状态。***

![a line annotation](https://developers.itextpdf.com/sites/default/files/C04F03.png)
```
    PdfDocument pdf = new PdfDocument(new PdfWriter(dest));
    PdfPage page = pdf.addNewPage();
    PdfArray lineEndings = new PdfArray();
    lineEndings.add(new PdfName("Diamond"));
    lineEndings.add(new PdfName("Diamond"));
    PdfAnnotation annotation = new PdfLineAnnotation(
        new Rectangle(0, 0),
        new float[]{20, 790, page.getPageSize().getWidth() - 20, 790})
            .setLineEndingStyles((lineEndings))
            .setContentsAsCaption(true)
            .setTitle(new PdfString("iText"))
            .setContents("The example of line annotation")
            .setColor(Color.BLUE);
    page.addAnnotation(annotation);
    pdf.close();
```

![a markup annotation](https://developers.itextpdf.com/sites/default/files/C04F04.png)
```
    PdfAnnotation ann = PdfTextMarkupAnnotation.createHighLight(
            new Rectangle(105, 790, 64, 10),
            new float[]{169, 790, 105, 790, 169, 800, 105, 800})
        .setColor(Color.YELLOW)
        .setTitle(new PdfString("Hello!"))
        .setContents(new PdfString("I'm a popup."))
        .setTitle(new PdfString("iText"))
        .setOpen(true)
        .setRectangle(new PdfArray(new float[]{100, 600, 200, 100}));
    pdf.getFirstPage().addAnnotation(ann);
```

***会在以后的章节中有专门的教程来讲解这些注释相关的代码***

## 创建交互的表格

![an interactive form](https://developers.itextpdf.com/sites/default/files/C04F05.png)

  虽然个人觉得交互表格部分很有趣，但是有以下几条理由让我决定不再花费时间去整理相关资料
   
    1. 大多数情况下，HTML的表单更为动态，方便
    2. 即使是教程中也讲到，几乎不会使用这种技术
    3. 对于我们报告而言不会涉及交互表单相关内容
    
  但还是有优点的
    
    1. 对于格式严格的表单，这种技术会更适用
    2. 不作为收集数据的表单，而是作为模板使用时更为方便


# 操作已存在的PDF
***在第四章中创建交互式表格被跳过了，但是其中有一部分关于操作已存在表格的内容需要特别说明，和第五章的内容有联系，因此在第五章中进行说明***
![a filled-out interactive form](https://developers.itextpdf.com/sites/default/files/C04F06_0.png)
```
    PdfDocument pdf = new PdfDocument(
        new PdfReader(src), new PdfWriter(dest));
    PdfAcroForm form = PdfAcroForm.getAcroForm(pdf, true);
    Map<String, PdfFormField> fields = form.getFormFields();
    fields.get("name").setValue("James Bond");
    fields.get("language").setValue("English");
    fields.get("experience1").setValue("Off");
    fields.get("experience2").setValue("Yes");
    fields.get("experience3").setValue("Yes");
    fields.get("shift").setValue("Any");
    fields.get("info").setValue("I was 38 years old when I became an MI6 agent.");
    // form.flattenFields(); // 如果你不希望终端修改这些选项
    pdf.close();
```

## 添加ann和content
![an updated form](https://developers.itextpdf.com/sites/default/files/C05F01_1.png)
```
    PdfDocument pdfDoc =
        new PdfDocument(new PdfReader(src), new PdfWriter(dest));
    // add content
    pdfDoc.close();
```
***
```
    PdfAnnotation ann = new PdfTextAnnotation(new Rectangle(400, 795, 0, 0))
        .setTitle(new PdfString("iText"))
        .setContents("Please, fill out the form.")
        .setOpen(true);
    pdfDoc.getFirstPage().addAnnotation(ann);
```
***
```
    PdfCanvas canvas = new PdfCanvas(pdfDoc.getFirstPage());
    canvas.beginText().setFontAndSize(
            PdfFontFactory.createFont(FontConstants.HELVETICA), 12)
            .moveText(265, 597)
            .showText("I agree to the terms and conditions.")
            .endText();
```

## 添加页眉，页脚和水印
![UFO sightings report](https://developers.itextpdf.com/sites/default/files/C05F04_1.png)
![UFO sightings report with header, footer, and watermark](https://developers.itextpdf.com/sites/default/files/C05F05_0_0.png)
* 在第三章中，在添加页脚的时候，由于并不知道总的页码数，所以无法添加形如"1 of 4"的页脚，当操作已经存在的PDF的过程中，就可以获取到总的页码数了

***当创建新的PDF的时候，可以在总页码数的位置放置占位符，当完成了所有的PDF的内容的时候，用实际的页码数代替占位符即可实现上述效果，但是在当前入门教程中不作说明***

```
    PdfDocument pdfDoc =
        new PdfDocument(new PdfReader(src), new PdfWriter(dest));
    Document document = new Document(pdfDoc);
    Rectangle pageSize;
    PdfCanvas canvas;
    int n = pdfDoc.getNumberOfPages();
    for (int i = 1; i <= n; i++) {
        PdfPage page = pdfDoc.getPage(i);
        pageSize = page.getPageSize();
        canvas = new PdfCanvas(page);
        // add new content
    }
    pdfDoc.close();
```
***
```
    //Draw header text
    canvas.beginText().setFontAndSize(
            PdfFontFactory.createFont(FontConstants.HELVETICA), 7)
            .moveText(pageSize.getWidth() / 2 - 24, pageSize.getHeight() - 10)
            .showText("I want to believe")
            .endText();
    //Draw footer line
    canvas.setStrokeColor(Color.BLACK)
            .setLineWidth(.2f)
            .moveTo(pageSize.getWidth() / 2 - 30, 20)
            .lineTo(pageSize.getWidth() / 2 + 30, 20).stroke();
    //Draw page number
    canvas.beginText().setFontAndSize(
            PdfFontFactory.createFont(FontConstants.HELVETICA), 7)
            .moveText(pageSize.getWidth() / 2 - 7, 10)
            .showText(String.valueOf(i))
            .showText(" of ")
            .showText(String.valueOf(n))
            .endText();
    //Draw watermark
    Paragraph p = new Paragraph("CONFIDENTIAL").setFontSize(60);
    canvas.saveState();
    PdfExtGState gs1 = new PdfExtGState().setFillOpacity(0.2f);
    canvas.setExtGState(gs1);
    document.showTextAligned(p,
            pageSize.getWidth() / 2, pageSize.getHeight() / 2,
            pdfDoc.getPageNumber(page),
            TextAlignment.CENTER, VerticalAlignment.MIDDLE, 45);
    canvas.restoreState();
```

1. 添加页眉
2. 添加页脚处横线
3. 添加页脚内容
4. 添加水印
  ***当你已经使用了PdfCanvas时，itext会察觉到，于是有了document.showTextAligned(p,...)的写法***
  
## 改变页面的大小和方向
![changed page size and orientation](https://developers.itextpdf.com/sites/default/files/C05F06_1.png)
```
    PdfDocument pdfDoc =
        new PdfDocument(new PdfReader(src), new PdfWriter(dest));
    float margin = 72;
    for (int i = 1; i <= pdfDoc.getNumberOfPages(); i++) {
        PdfPage page = pdfDoc.getPage(i);
        // change page size
        Rectangle mediaBox = page.getMediaBox();
        Rectangle newMediaBox = new Rectangle(
                mediaBox.getLeft() - margin, mediaBox.getBottom() - margin,
                mediaBox.getWidth() + margin * 2, mediaBox.getHeight() + margin * 2);
        page.setMediaBox(newMediaBox);
        // add border
        PdfCanvas over = new PdfCanvas(page);
        over.setStrokeColor(Color.GRAY);
        over.rectangle(mediaBox.getLeft(), mediaBox.getBottom(),
                mediaBox.getWidth(), mediaBox.getHeight());
        over.stroke();
        // change rotation of the even pages
        if (i % 2 == 0) {
            page.setRotation(180);
        }
    }
    pdfDoc.close();
```
***操作已有的PDF文件需要一些PDF相关的知识，比如上述代码中出现的MediaBox***


# 利用已经存在的PDF

## 缩放，拼接和平移
![Golden Gate Bridge, original size 16.54 x 11.69 in](https://developers.itextpdf.com/sites/default/files/C06F01_1.png)
```
    PdfDocument pdf = new PdfDocument(new PdfWriter(dest));
    PdfDocument origPdf = new PdfDocument(new PdfReader(src));
    PdfPage origPage = origPdf.getPage(1);
    Rectangle orig = origPage.getPageSizeWithRotation();
     
    //Add A4 page
    PdfPage page = pdf.addNewPage(PageSize.A4.rotate());
    //Shrink original page content using transformation matrix
    PdfCanvas canvas = new PdfCanvas(page);
    AffineTransform transformationMatrix = AffineTransform.getScaleInstance(
        page.getPageSize().getWidth() / orig.getWidth(),
        page.getPageSize().getHeight() / orig.getHeight());
    canvas.concatMatrix(transformationMatrix);
    PdfFormXObject pageCopy = origPage.copyAsFormXObject(pdf);
    canvas.addXObject(pageCopy, 0, 0);
     
    //Add page with original size
    pdf.addPage(origPage.copyTo(pdf));
     
    //Add A2 page
    page = pdf.addNewPage(PageSize.A2.rotate());
    //Scale original page content using transformation matrix
    canvas = new PdfCanvas(page);
    transformationMatrix = AffineTransform.getScaleInstance(
        page.getPageSize().getWidth() / orig.getWidth(),
        page.getPageSize().getHeight() / orig.getHeight());
    canvas.concatMatrix(transformationMatrix);
    canvas.addXObject(pageCopy, 0, 0);
     
    pdf.close();
    origPdf.close();
```
* 上述代码效果是将上图缩放不同规格添加到PDF中

## 组装PDF
### 合并PDF
```
    PdfDocument pdf = new PdfDocument(new PdfWriter(dest));
    PdfMerger merger = new PdfMerger(pdf);
    //Add pages from the first document
    PdfDocument firstSourcePdf = new PdfDocument(new PdfReader(SRC1));
    merger.merge(firstSourcePdf, 1, firstSourcePdf.getNumberOfPages());
     //Add pages from the second pdf document
    PdfDocument secondSourcePdf = new PdfDocument(new PdfReader(SRC2));
    merger.merge(secondSourcePdf, 1, secondSourcePdf.getNumberOfPages());
    // merge and close
    merger.close();
    firstSourcePdf.close();
    secondSourcePdf.close();
    pdf.close();
```
**将两个PDF文档合并成一个PDF文档**

```
    PdfDocument pdf = new PdfDocument(new PdfWriter(dest));
    PdfMerger merger = new PdfMerger(pdf);
    PdfDocument firstSourcePdf = new PdfDocument(new PdfReader(SRC1));
    merger.merge(firstSourcePdf, Arrays.asList(1, 5, 7, 1));
    PdfDocument secondSourcePdf = new PdfDocument(new PdfReader(SRC2));
    merger.merge(secondSourcePdf, Arrays.asList(1, 15));
    merger.close();
    firstSourcePdf.close();
    secondSourcePdf.close();
    pdf.close();
```
**将选定的页添加到合并文档中**

### 将页面添加到PDF中
```
    public static final Map<String, Integer> TheRevenantNominations =
        new TreeMap<String, Integer>();
    static {
        TheRevenantNominations.put("Performance by an actor in a leading role", 4);
        TheRevenantNominations.put(
            "Performance by an actor in a supporting role", 4);
        TheRevenantNominations.put("Achievement in cinematography", 4);
        TheRevenantNominations.put("Achievement in costume design", 5);
        TheRevenantNominations.put("Achievement in directing", 5);
        TheRevenantNominations.put("Achievement in film editing", 6);
        TheRevenantNominations.put("Achievement in makeup and hairstyling", 7);
        TheRevenantNominations.put("Best motion picture of the year", 8);
        TheRevenantNominations.put("Achievement in production design", 8);
        TheRevenantNominations.put("Achievement in sound editing", 9);
        TheRevenantNominations.put("Achievement in sound mixing", 9);
        TheRevenantNominations.put("Achievement in visual effects", 10);
    }
```
* 定义一个红黑树

>>> TreeMap是一个有序的Map，在构造方法中可以有比较器参数，按照定义的比较规则有序排列Map中的元素，没有参数的情况下使用的就是默认的比较器，
在生成PDF中你会发现目录是按照字母顺序排列的。

```
    PdfDocument firstSourcePdf = new PdfDocument(new PdfReader(SRC1));
    for (Map.Entry<String, Integer> entry : TheRevenantNominations.entrySet()) {
        //Copy page
        PdfPage page  = firstSourcePdf.getPage(entry.getValue()).copyTo(pdfDoc);
        pdfDoc.addPage(page);
        //Overwrite page number
        Text text = new Text(String.format(
            "Page %d", pdfDoc.getNumberOfPages() - 1));
        text.setBackgroundColor(Color.WHITE);
        document.add(new Paragraph(text).setFixedPosition(
                pdfDoc.getNumberOfPages(), 549, 742, 100));
        //Add destination
        String destinationKey = "p" + (pdfDoc.getNumberOfPages() - 1);
        PdfArray destinationArray = new PdfArray();
        destinationArray.add(page.getPdfObject());
        destinationArray.add(PdfName.XYZ);
        destinationArray.add(new PdfNumber(0));
        destinationArray.add(new PdfNumber(page.getMediaBox().getHeight()));
        destinationArray.add(new PdfNumber(1));
        pdfDoc.addNameDestination(destinationKey, destinationArray);
        //Add TOC line with bookmark
        Paragraph p = new Paragraph();
        p.addTabStops(
            new TabStop(540, TabAlignment.RIGHT, new DottedLine()));
        p.add(entry.getKey());
        p.add(new Tab());
        p.add(String.valueOf(pdfDoc.getNumberOfPages() - 1));
        p.setProperty(Property.ACTION, PdfAction.createGoTo(destinationKey));
        document.add(p);
    }
    firstSourcePdf.close();
```
1. 创建包含所有“提名信息”的PdfDocument。
2. 遍历之前的TreeMap。
3. 获得相应页面的内容并复制到目标PDF中。
4. Text元素组成页码，页码减一是因为第一页不被包含在计数页码中。
5. 设置text的背景颜色为Color.WHITE。这将会绘制一个和Text相同规格的不透明的白色矩形。用它来覆盖原有的页码。
6. 将text放置到当前页面的固定位置。位置的的坐标为X=549，Y=742，宽度为100单位。
7. 创建一个用于命名目标的key
8. 创建一个PdfArray，它包含了目标的相关信息
   1. 相关的Page对象
   2. 定义X，Y坐标和缩放
   3. X坐标
   4. Y坐标
   5. 缩放
9. 目标key与目标页面信息关联
10. 添加目录内容
11. 为内容添加action（实现点击跳转到对应的页面）

```
    //Add the last page
    PdfDocument secondSourcePdf = new PdfDocument(new PdfReader(SRC2));
    PdfPage page  = secondSourcePdf.getPage(1).copyTo(pdfDoc);
    pdfDoc.addPage(page);
    //Add destination
    PdfArray destinationArray = new PdfArray();
    destinationArray.add(page.getPdfObject());
    destinationArray.add(PdfName.XYZ);
    destinationArray.add(new PdfNumber(0));
    destinationArray.add(new PdfNumber(page.getMediaBox().getHeight()));
    destinationArray.add(new PdfNumber(1));
    pdfDoc.addNameDestination("checklist", destinationArray);
    //Add TOC line with bookmark
    Paragraph p = new Paragraph();
    p.addTabStops(new TabStop(540, TabAlignment.RIGHT, new DottedLine()));
    p.add("Oscars\u00ae 2016 Movie Checklist");
    p.add(new Tab());
    p.add(String.valueOf(pdfDoc.getNumberOfPages() - 1));
    p.setProperty(Property.ACTION, PdfAction.createGoTo("checklist"));
    document.add(p);
    secondSourcePdf.close();
    // close the document
    document.close();
```

***上边的例子在实际应用中一般不会出现。假设TOC只由一个页面组成，在document中添加更多的行，将会发现一个奇怪的现象：
文本内容不符合一页的大小，将会添加第二个页面。但是第二个页面又不会是一个新的页面，它将在第一个页面循环添加内容。也就是说，
第一页之前写的内容将会被覆盖。这个问题是可以解决的，接下来的教程中会提到。***

# installing itext7

```
      <dependencies>
        <dependency>
            <groupId>com.itextpdf</groupId>
            <artifactId>kernel</artifactId>
            <version>7.0.4</version>
        </dependency>
        <dependency>
            <groupId>com.itextpdf</groupId>
            <artifactId>io</artifactId>
            <version>7.0.4</version>
        </dependency>
        <dependency>
            <groupId>com.itextpdf</groupId>
            <artifactId>layout</artifactId>
            <version>7.0.4</version>
        </dependency>
        <dependency>
            <groupId>com.itextpdf</groupId>
            <artifactId>forms</artifactId>
            <version>7.0.4</version>
        </dependency>
        <dependency>
            <groupId>com.itextpdf</groupId>
            <artifactId>pdfa</artifactId>
            <version>7.0.4</version>
        </dependency>
        <dependency>
            <groupId>com.itextpdf</groupId>
            <artifactId>pdftest</artifactId>
            <version>7.0.4</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>1.7.18</version>
        </dependency>
    </dependencies>
```

***

  有些扩展功能必须要在获得商业许可证后才能使用,因为是闭源的,它也不在maven仓库中
  
  **获取许可证方法**
  ```
   <dependency>
        <groupId>com.itextpdf</groupId>
        <artifactId>itext-licensekey</artifactId>
        <version>2.0.4</version>
    </dependency>
  ```
  
  **使用扩展功能**
  ```
    <repositories>
     <repository>
          <id>itext</id>
          <name>iText Repository - releases</name>
          <url>https://repo.itextsupport.com/releases</url>
      </repository>
    </repositories>
  ```




































