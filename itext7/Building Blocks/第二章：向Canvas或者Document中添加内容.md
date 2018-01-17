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
