# 向Canvas或者Document中添加内容

## 使用Canvas向Rectangle中添加内容
![Adding text inside a rectangle](https://developers.itextpdf.com/sites/default/files/C02F01_0.png)
```
    PdfDocument pdf = new PdfDocument(new PdfWriter(dest));
    PdfPage page = pdf.addNewPage();
    PdfCanvas pdfCanvas = new PdfCanvas(page);
    Rectangle rectangle = new Rectangle(36, 650, 100, 100);
    pdfCanvas.rectangle(rectangle);
    pdfCanvas.stroke();
    Canvas canvas = new Canvas(pdfCanvas, pdf, rectangle);
    PdfFont font = PdfFontFactory.createFont(FontConstants.TIMES_ROMAN);
    PdfFont bold = PdfFontFactory.createFont(FontConstants.TIMES_BOLD);
    Text title =
        new Text("The Strange Case of Dr. Jekyll and Mr. Hyde").setFont(bold);
    Text author = new Text("Robert Louis Stevenson").setFont(font);
    Paragraph p = new Paragraph().add(title).add(" by ").add(author);
    canvas.add(p);
    canvas.close();
    pdf.close();
```
* 这里没有使用Document对象，所以使用pdf.addNewPage()来创建新的页面。

![Adding text that doesn't fit a rectangle](https://developers.itextpdf.com/sites/default/files/C02F02_0.png)
![Filling a rectangle with text](https://developers.itextpdf.com/sites/default/files/C02F03_0.png)
* 如果所添加的内容超过了矩形的范围，那么多出的部分就会无法显示，为了让所有的内容都能显示出来，需要不断的多次尝试修改矩形的规格。
```
class MyCanvasRenderer extends CanvasRenderer {
    protected boolean full = false;
 
    private MyCanvasRenderer(Canvas canvas) {
        super(canvas);
    }
 
    @Override
    public void addChild(IRenderer renderer) {
        super.addChild(renderer);
        full = Boolean.TRUE.equals(getPropertyAsBoolean(Property.FULL));
    }
 
    public boolean isFull() {
        return full;
    }
}
```
* 上述代码可以判断矩形是否被占满了
```
    Rectangle rectangle = new Rectangle(36, 500, 100, 250);
    Canvas canvas = new Canvas(pdfCanvas, pdf, rectangle);
    MyCanvasRenderer renderer = new MyCanvasRenderer(canvas);
    canvas.setRenderer(renderer);
    PdfFont font = PdfFontFactory.createFont(FontConstants.TIMES_ROMAN);
    PdfFont bold = PdfFontFactory.createFont(FontConstants.TIMES_BOLD);
    Text title =
        new Text("The Strange Case of Dr. Jekyll and Mr. Hyde").setFont(bold);
    Text author = new Text("Robert Louis Stevenson").setFont(font);
    Paragraph p = new Paragraph().add(title).add(" by ").add(author);
    while (!renderer.isFull())
        canvas.add(p);
    canvas.close();
```
***Document和Canvas可以使用边框，但不能够修改边框，能使用是因为它继承了ElementPropertyContainer类，不能使用是因为它没有实现相关的接口***
![Adding content to the previous page](https://developers.itextpdf.com/sites/default/files/C02F04_0.png)
```
    PdfPage page2 = pdf.addNewPage();
    PdfCanvas pdfCanvas2 = new PdfCanvas(page2);
    Canvas canvas2 = new Canvas(pdfCanvas2, pdf, rectangle);
    canvas2.add(new Paragraph("Dr. Jekyll and Mr. Hyde"));
    canvas2.close();
```
>>>
```
    PdfPage page1 = pdf.getFirstPage();
    PdfCanvas pdfCanvas1 = new PdfCanvas(
        page1.newContentStreamBefore(), page1.getResources(), pdf);
    rectangle = new Rectangle(100, 700, 100, 100);
    pdfCanvas1.saveState()
            .setFillColor(Color.CYAN)
            .rectangle(rectangle)
            .fill()
            .restoreState();
    Canvas canvas = new Canvas(pdfCanvas1, pdf, rectangle);
    canvas.add(new Paragraph("Dr. Jekyll and Mr. Hyde"));
    canvas.close();
```
* 先添加左边的透明矩形，后添加右边有背景颜色的矩形。
* 使用getFirstPage()获取第一页页面对象

***getFirstPage()方法是getPage()方法的常用版本，它允许在PdfDocument对象关闭前获取页面对象***
* PdfCanvas构造方法中的参数：
  1. PdfStream实例--如果你想绘制现有内容底层内容，可以使用newContentStreamBefore()，反之可以使用getContentStreamAfter()。
  你也可以获取已经存在的content stream：getContentStream()
  2. PdfResources实例--一个content stream本身不一定能够渲染一个页面。每个页面的完成都可能会用到例如fonts和images这些外部资源。  
  3. PdfDocument实例。

***这是itext7中的新功能，获取已经完成的页面的content stream并向其中添加内容***

**目前为止，已经使用了Canvas类向PdfCanvas中添加内容，在第七章中，将会演示使用Canvas向PdfFormXObject中添加内容。
  XObject代表着PDF的content stream，可以多次在一个相同或不同的页面中使用它。**
  
## 使用Document类将text转换为PDF 
![Text file with the Jekyll and Hyde story](https://developers.itextpdf.com/sites/default/files/C02F05.png)
![First attempt to convert txt to PDF](https://developers.itextpdf.com/sites/default/files/C02F06.png)
```
    PdfDocument pdf = new PdfDocument(new PdfWriter(dest));
    Document document = new Document(pdf);
    BufferedReader br = new BufferedReader(new FileReader(SRC));
    String line;
    while ((line = br.readLine()) != null) {
        document.add(new Paragraph(line));
    }
    document.close();
```
  似乎结果看起来已经不错了，不过还有可改进的地方。
![Second attempt to convert txt to PDF](https://developers.itextpdf.com/sites/default/files/C02F07.png)
```
    document.setTextAlignment(TextAlignment.JUSTIFIED)
        .setHyphenation(new HyphenationConfig("en", "uk", 3, 3));
```
* setTextAlignment()：在Document级别设置了对齐方式
* setHyphenation()：定义连字符规则
  
    以British English来看待document内容，并且设置连字符前和连字符后尽量都有至少3个字符（末尾换行时体现出来的）

***itext5中是不能够在Document级别上定义相关属性的***

![Third attempt to convert txt to PDF](https://developers.itextpdf.com/sites/default/files/C02F08.png)
```
    Document document = new Document(pdf);
    PdfFont font = PdfFontFactory.createFont(FontConstants.TIMES_ROMAN);
    PdfFont bold = PdfFontFactory.createFont(FontConstants.HELVETICA_BOLD);
    document.setTextAlignment(TextAlignment.JUSTIFIED)
        .setHyphenation(new HyphenationConfig("en", "uk", 3, 3))
        .setFont(font)
        .setFontSize(11);
```
* 修改document的字体和字号
```
    BufferedReader br = new BufferedReader(new FileReader(SRC));
    String line;
    Paragraph p;
    boolean title = true;
    while ((line = br.readLine()) != null) {
        p = new Paragraph(line);
        p.setKeepTogether(true);
        if (title) {
            p.setFont(bold).setFontSize(12);
            title = false;
        }
        else {
            p.setFirstLineIndent(36);
        }
        if (line.isEmpty()) {
            p.setMarginBottom(12);
            title = true;
        }
        else {
            p.setMarginBottom(0);
        }
        document.add(p);
    }
```
* Boolean变量"title"，初始化为true，因为文章的第一行是title。
* setKeepTogether()方法可以让同一个段落不会分布在不同的页面。如果一个段落不能够适应当前的页面，将会添加到下一个页面中，如果仍然不能适应下一个页面，这种情况下就会被强行分割，一部分添加到当前页面，另一部分添加到下一个页面或接下来的多个页面中。
* 如果title的值为true，会将字体改为12号，并将title设置为false
* 如果读到了空字符行，定义下边距为12并重置title变量为true

## 更改Document renderer
![Rendering the text in two columns](https://developers.itextpdf.com/sites/default/files/C02F09.png)
```
    float offSet = 36;
    float gutter = 23;
    float columnWidth = (PageSize.A4.getWidth() - offSet * 2) / 2 - gutter;
    float columnHeight = PageSize.A4.getHeight() - offSet * 2;
    Rectangle[] columns = {
        new Rectangle(offSet, offSet, columnWidth, columnHeight),
        new Rectangle(
            offSet + columnWidth + gutter, offSet, columnWidth, columnHeight)};
    document.setRenderer(new ColumnDocumentRenderer(document, columns)); 
```
* 创建一个Rectangle对象数组
* setRenderer()方法设置渲染器
![The effect of an AreaBreak of type NEXT_AREA](https://developers.itextpdf.com/sites/default/files/C02F10.png)
```
    BufferedReader br = new BufferedReader(new FileReader(SRC));
    String line;
    Paragraph p;
    boolean title = true;
    AreaBreak nextArea = new AreaBreak(AreaBreakType.NEXT_AREA);
    while ((line = br.readLine()) != null) {
        p = new Paragraph(line);
        if (title) {
            p.setFont(bold).setFontSize(12);
            title = false;
        }
        else {
            p.setFirstLineIndent(36);
        }
        if (line.isEmpty()) {
            document.add(nextArea);
            title = true;
        }
        document.add(p);
    }
```
* AreaBreak对象--这是一个布局对象，它终止当前内容区域，并创建一个新的区域。创建NEXT_AREA类型的AreaBreak，并在每个新章节前使用它。

![The effect of an AreaBreak of type NEXT_PAGE](https://developers.itextpdf.com/sites/default/files/C02F11.png)
```
AreaBreak nextPage = new AreaBreak(AreaBreakType.NEXT_PAGE);
```
* 如果没有AreaBreak，新的一章总会贴着之前一章的正文结束的位置。
* NEXT_PAGE类型的AreaBreak会使新的一章在新的一页开始。

***通常情况下，下一页的页面大小和当前页的页面大小时一致的，如果想要创建新的不同规格的页面，可以使用new AreaBreak(PageSize.A3)***

## 切换不同的渲染器
![Different renderers in the same document](https://developers.itextpdf.com/sites/default/files/F0212.png)
```
    public void createPdf(String dest) throws IOException {
        PdfDocument pdf = new PdfDocument(new PdfWriter(dest));
        Document document = new Document(pdf);
        Paragraph p = new Paragraph()
            .add("Be prepared to read a story about a London lawyer "
            + "named Gabriel John Utterson who investigates strange "
            + "occurrences between his old friend, Dr. Henry Jekyll, "
            + "and the evil Edward Hyde.");
        document.add(p);
        document.add(new AreaBreak(AreaBreakType.NEXT_PAGE));
        ... // Define column areas
        document.setRenderer(new ColumnDocumentRenderer(document, columns));
        document.add(new AreaBreak(AreaBreakType.LAST_PAGE));   
        ... // Add novel in two columns
        document.add(new AreaBreak(AreaBreakType.NEXT_PAGE));
        document.setRenderer(new DocumentRenderer(document)); 
        document.add(new AreaBreak(AreaBreakType.LAST_PAGE));
        p = new Paragraph()
            .add("This was the story about the London lawyer "
            + "named Gabriel John Utterson who investigates strange "
            + "occurrences between his old friend, Dr. Henry Jekyll, "
            + "and the evil Edward Hyde. THE END!");
        document.add(p);
        document.close();
    }
```
* 在第一页中使用DocumentRenderer，第二页中使用ColumnDocumentRenderer。

***为什么会在每次创建新的renderer的时候都要创建新的LAST_PAGE类型的AreaBreak呢？
   实际上每次创建新的DocumentRenderer的时候，itext都会跳转到第一页，这样一来就能允许你在任何页面使用这个渲染器。
   但是呢，这就要求itext的内容不能输出到OutputStream中，否则你就没法修改之前的页面了。
   在这个例子中，我们不需要对之前的页面进行修改，所以每次创建新的renderer的时候都会跳转到LAST_PAGE。
   这样一来，就不会出现把之前写好的内容覆盖掉的情况了，不然的话，从第一页起，都会被新的renderer渲染***
   
## Flushing Document renderer

**在API中，可以看到Canvas，Document等对象的构造方法中都有一个Boolean类型的参数，叫做immediateFlush。目前为止用到的例子中都没有说明，实际上
我们一直使用的是它的默认值：true，所有的内容都在添加后立即flush**

  一般情况下延迟flush的原因有三个：
  1. 在添加内容后想要改变布局
  2. 在添加内容后想要改变内容
  3. 想要向之前的页面添加内容
  
***在itext5中，Document会在页面被填满后立即flush，一旦flush之后，就没有任何方法可以改变内容的布局了***

![Moving a column to the middle of the page](https://developers.itextpdf.com/sites/default/files/C02F13.png)
  想要把单独出现的一列移到页面的中间，但我们不知道这种情况什么时候会发生。这意味着我们不能够立即渲染内容，所以我们要延迟flush。
```
    class MyColumnRenderer extends DocumentRenderer {
        protected int nextAreaNumber;
        protected final Rectangle[] columns;
        protected int currentAreaNumber;
        protected Set<Integer> moveColumn = new HashSet<>();
     
        public MyColumnRenderer(Document document, Rectangle[] columns) {
            super(document, false);
            this.columns = columns;
        }
     
        @Override
        protected LayoutArea updateCurrentArea(LayoutResult overflowResult) {
            if (overflowResult != null
                && overflowResult.getAreaBreak() != null
                && overflowResult.getAreaBreak().getType()
                    != AreaBreakType.NEXT_AREA) {
                nextAreaNumber = 0;
            }
            if (nextAreaNumber % columns.length == 0) {
                super.updateCurrentArea(overflowResult);
            }
            currentAreaNumber = nextAreaNumber + 1;
            return (currentArea = new LayoutArea(currentPageNumber,
                columns[nextAreaNumber++ % columns.length].clone()));
        }
     
        @Override
        protected PageSize addNewPage(PageSize customPageSize) {
            if (currentAreaNumber != nextAreaNumber
                && currentAreaNumber % columns.length != 0)
                moveColumn.add(currentPageNumber - 1);
            return super.addNewPage(customPageSize);
        }
     
        @Override
        protected void flushSingleRenderer(IRenderer resultRenderer) {
            int pageNum = resultRenderer.getOccupiedArea().getPageNumber();
            if (moveColumn.contains(pageNum)) {
                resultRenderer.move(columns[0].getWidth() / 2, 0);
            }
            super.flushSingleRenderer(resultRenderer);
        }
    }
```
* 复用了ColumnDocumentRenderer中的两个成员变量，并定义了两个额外变量
  1. nextAreaNumber：跟踪计数有多少列
  2. columns：存储每列的位置和尺寸
  3. currentAreaNumber：记录当前有多少列  
  4. moveColumn：存储单列页面的页码的集合
* MyColumnRenderer的构造方法，继承父类的构造方法并将immediateFlush参数设置为false  
* updateCurrentArea(LayoutResult overflowResult)：基本和父类方法相同。区别在于设置currentAreaNumber的值为nextAreaNumber+1。每当创建新的列的时候，这个方法都会被调用。每当页面引入page break的时候，currentAreaNumber会被设置为0。
* 重写了newPage()方法。这个方法会在每次创建新的页面的时候被触发。我们用这个方法去检查前一页是否是只有一列。如果currentAreaNumber和nextAreaNumber不相等，且currentAreaNumber是偶数（偶数为判断依据是因为这个例子中是两列），这就表明了前一页只有一列，于是将（currentPageNumber-1）添加到moveColumns集合中。
* 重写了flushSingleRenderer()方法。这个方法负责渲染内容，如果immediateFlush为true，这个方法会被自动调用。如果immediateFlush是false，就必须由我们来触发这个方法。重写这个方法是因为想要移动IRenderer的坐标系。

```
    Rectangle[] columns = {
        new Rectangle(offSet, offSet, columnWidth, columnHeight),
        new Rectangle(
            offSet + columnWidth + gutter, offSet, columnWidth, columnHeight)};
    DocumentRenderer renderer = new MyColumnRenderer(document, columns);
    document.setRenderer(renderer);
    renderer.flush();
    document.close();
```
* 如果没有使用renderer.flush()方法，那么在你的文档中只会出现空的页面。
* renderer.flush()方法可以调用flushSingleRenderer()方法

## 改变先前添加的内容

![Start by showing the total number of pages](https://developers.itextpdf.com/sites/default/files/C02F14.png)
  
  图中第一行"This document has 34 pages."
  
  ***在之前都是一行一行的读取内容，是怎么得知一共是34页呢？***

```
    Document document = new Document(pdf, PageSize.A4, false);
    Text totalPages = new Text("This document has {totalpages} pages.");
    IRenderer renderer = new TextRenderer(totalPages);
    totalPages.setNextRenderer(renderer);
    document.add(new Paragraph(totalPages));
```
* 使用占位符{totalpages}来代替实际的总页码数。
```
    String total = renderer.toString().replace("{totalpages}",
        String.valueOf(pdf.getNumberOfPages()));
    ((TextRenderer)renderer).setText(total);
    ((Text)renderer.getModelElement()).setNextRenderer(renderer);
    document.relayout();
    document.close();
```
* 检索Text对象的内容，并替换占位符。
* relayout()：完成替换

## 添加形如X of Y样式的页脚
![Page X of Y footer](https://developers.itextpdf.com/sites/default/files/C02F15.png)
```
    Document document = new Document(pdf, PageSize.A4, false);
        int n = pdf.getNumberOfPages();
    Paragraph footer;
    for (int page = 1; page <= n; page++) {
        footer = new Paragraph(String.format("Page %s of %s", page, n));
        document.showTextAligned(footer, 297.5f, 20, page,
            TextAlignment.CENTER, VerticalAlignment.MIDDLE, 0);
    }
    document.close();
```
* showTextAligned()：可以在任何页面的绝对位置添加文字内容，并且可以选择水平和垂直的对齐方式和倾斜角度。
* 这里并不需要改变PDF的布局，所以不需要relayout()方法。
* 仅在immediateFlush参数为false时可用，否则会报错

    Exception in thread "main" java.lang.NullPointerException at com.itextpdf.kernel.pdf.PdfDictionary.get(PdfDictionary.java)

## 使用showTextAligned添加text
**在RootElement类中实现了许多的showTextAligned()方法。这些方法可以被Canvas和Document对象使用来在绝对位置添加text。如果位置或者内容的参数不合适，内容可能会被分割或者无法在页面中正确显示。**
!Text added at absolute positions[](https://developers.itextpdf.com/sites/default/files/C02F16_0.png)
```
    Paragraph title = new Paragraph("The Strange Case of Dr. Jekyll and Mr. Hyde");
    document.showTextAligned(title, 36, 806, TextAlignment.LEFT);
    Paragraph author = new Paragraph("by Robert Louis Stevenson");
    document.showTextAligned(author, 36, 806,
        TextAlignment.LEFT, VerticalAlignment.TOP);
    document.showTextAligned("Jekyll", 300, 800,
        TextAlignment.CENTER, 0.5f * (float)Math.PI);
    document.showTextAligned("Hyde", 300, 800,
        TextAlignment.CENTER, -0.5f * (float)Math.PI);
    document.showTextAligned("Jekyll", 350, 800,
        TextAlignment.CENTER, VerticalAlignment.TOP, 0.5f * (float)Math.PI);
    document.showTextAligned("Hyde", 350, 800,
        TextAlignment.CENTER, VerticalAlignment.TOP, -0.5f * (float)Math.PI);
    document.showTextAligned("Jekyll", 400, 800,
        TextAlignment.CENTER, VerticalAlignment.MIDDLE, 0.5f * (float)Math.PI);
    document.showTextAligned("Hyde", 400, 800,
        TextAlignment.CENTER, VerticalAlignment.MIDDLE, -0.5f * (float)Math.PI);
```

## 使用itext7扩展元素
**itext7的核心代码库是开源的，可以免费使用，但如果想把它用在闭源项目中，你必须购买相关的许可证。**

***开源并不代表着免费~***

***用了一些例子说明在某些情况下购买商业许可证的重要性，itext公司为此也将部分代码闭源，只有在购买后才可以使用***
