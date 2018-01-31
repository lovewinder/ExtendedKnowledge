# ���ܻ���ģ��

## �����ĵ�
```
    PdfWriter writer = new PdfWriter(dest);
    PdfDocument pdf = new PdfDocument(writer);
    Document document = new Document(pdf);
    document.add(new Paragraph("Hello World!"));
    document.close();
```
* ����**PdfWriter**�������Դ���һ��pdf�ļ�������֪���ĵ���ʲô�йأ���������Ϊ�˱�֤�ĵ��Ľṹ�����������ʵ���У����췽���д�����һ��String��Ϊ�����������������ĵ���·�������磺*result/chapter01/hello_world.pdf*

    ������췽��ͬʱ���Խ���*OutputStream*��Ϊ������
    
    * ���Ҫдһ��webӦ�ó���,��������Ϊ*ServletOutputStream*
    
    * ���Ҫд���ڴ���,��������Ϊ*ByteArrayOutputStream*
      
* **PdfWriter**֪��ʲô��д�����ĵ�,��Ϊ��������**PdfDocument**
    **PdfDocument**������ӵ�����,�����ݷ��䵽��ͬ��ҳ��,���Ҹ����κ���������йص���Ϣ
    
* һ�����Ǵ�����**PdfWriter**��**PdfDocument**,���Ǿ��Ѿ�����˵ͼ�ģ��
    
    ����������**Document**����,��**PdfDocument**��Ϊ����
    
* ��������"Hello World"��**Paragraph**,Ȼ��paragraph��ӵ�**document**��   

* ���ر�document,�������PDF�Ĵ���

## ��Ӹ��Ӷ���
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
* iTextʹ��Helvetica��ΪĬ�����塣����ͨ������PdfFontʵ�����޸����壬����ͨ��PdfFontFactory��ȡPdfFontʵ����
* ������List��ʽ��Ӷ��䣬list���������������������塣ͨ��add(new ListItem())��list������ݡ�

## ����������ͼƬ
```
    Image fox = new Image(ImageDataFactory.create(FOX));
    Image dog = new Image(ImageDataFactory.create(DOG));
    Paragraph p = new Paragraph("The quick brown ")
                .add(fox)
                .add(" jumps over the lazy ")
                .add(dog);
    document.add(p);
```
* ��ImageDataFactory�д���һ��ͼƬ·�������������������ݴ���ͼƬ�����ͣ�jpg,png,gif,bmp...)����������Ǵ������PDF�п��õ�ͼƬ��
* ���õ���Imageʵ����ӵ������м��ɡ�

# �������ݿ�
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
* Ĭ�ϵ�ҳ���СΪA4��PageSize.A4.rotate()��ʾʹ�ú���ҳ��
* Ĭ�ϵ�ҳ�߾�Ϊ36���أ�setMargins(20,20,20,20)��������ҳ�߾ࣨ��
* Table�����ϱߴ����еĴ���������ķ�ʽ�У���ʾһ������9�У����ִ�����ÿһ�е���Կ�ȡ������ܿ��Ϊҳ���ȵ�100%��ȥҳ�߾ࡣ
* ��ȡ��һ����Ϊ��ͷ�����ñ�ͷ����Ϊbold����ȡʣ�µ����ݣ���Ϊ��ͨ���
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
* table������ӵı��
* line�����ж�ȡ���ı�����
* font������
* isHeader���Ƿ�Ϊ��ͷ

* table.addHeaderCell(cell)����ӱ�ͷ��Ԫ��
* table.addCell(cell)�������ͨ��Ԫ��

# ��ӵͼ�����

```
canvas.moveTo(-406,0)
      .lineTo(406,0)
      .stroke();
```

## ��canvas����
```
PdfDocument pdf = new PdfDocument(new PdfWriter(dest));
PageSize ps = PageSize.A4.rotate();
PdfPage Page = pdf.addNewPage(ps);
PdfCanvas canvas = new PdfCanvas(page);
// Draw the axes
pdf.close();
```
* ���Ȳ���ʹ��Document����
* ����PdfPageʵ����������PdfCanvasʵ��
* ʹ��canvas�������ͼ��
* �ر�PdfDocument

***����һ�������ر��ĵ���ͨ��document.close()����ɵģ�����û��Document���󣬲��ò�ͨ��pdf.close()�ر��ĵ�***

**��PDF�У�72��Ԫ����һӢ�ߡ�**

## ����ϵͳ��ת������
```
canvas.concatMatrix(1,0,0,1,ps.getWidth()/2,ps.getHeight()/2)
```
* ����ͨ��ת���������ƶ������λ��
* ��PDF�У�����ȥ�ƶ����󣬶���ȥ�ƶ�����ϵͳ�����µ�����ϵͳ�л��ƶ���
* ���Ƶ�Դ����ϵͳ��λ����ҳ�������

```
a  b  0
c  d  0
e  f  1
```
* ��������Զ�ǡ�0,0,1������Ϊ�Ƕ�ά�ռ�
* a�������ţ�b������ת��c�����������б��
* e��f�����������ƽ��λ��

## ͼ�ε�״̬
### ��������ϵ
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
* �����������Ϊ����˵����concatMatrix()�����ǿ��Խ�ʵ�ʵ�����ԭ�����޸Ĳ�Ӧ�õ�
* �������������������ӵ��ߣ�
    1. miter-������ӣ�Ĭ�ϣ�
    2. bevel-б�������
    3. round-Բ������
* ֮��ʹ��setLineJoinStyle(...)�ָ�canvas��ԭʼ״̬�����Ⲣ������ѵķ���
* �ڸ���canvas״̬֮ǰ��ʹ��saveState()���������Ϳ���ʹ������״̬��canvas���Ƹ���ͼ���ˣ����ʹ��restoreState()��canvas��״̬�ָ���saveState()֮ǰ��״̬
* stroke()��������������ͼ�λ������֮����ʹ��

>>> ��Щ�����Լ����Ҫ����saveState()��restoreState()Ҫ�ɶԳ���

### ��ɫ
```
Color grayColor = new DeviceCmyk(0.f,0.f,0.f,0.875f);
Color greenColor = new DeviceCmyk(1.f,0.f,1.f,0.176f);
Color blueColor = new DeviceCmyk(1.f,0.156f,0.f,0.118f);
```
* PDF������಻ͬ����ɫ�ռ䡣��itext�У�ÿһ�ֶ��ڲ�ͬ��class�б�ʵ�֣���õ��У�
  1. DeviceGray-ֻ��һ������ȷ��
  2. DeviceRgb-����������ȷ�����죬�̣�����
  3. DeviceCmyk-���ĸ�����ȷ�����࣬�죬�ƣ��ڣ�

**ע��ʹ��Color�����õİ���com.itextpdf.kernel.color������java.awt.Color**

### ��������
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
                  .lineTo(j,ps.getHeight() / 2 -15����
      }
}
canvas.stroke();
```
### ��������
```
canvas.setLineWidth(2).setStrokeColor(greenColor)
      .setLineDash(10,10,8)
      .moveTo(-(ps.getWidth() / 2 - 15),-(ps.getHeight() / 2 -15))
      .lineTo(ps.getWidth() / 2 - 15,ps.getHeight() / 2 -15).stroke();
```
* �ж��ֻ������ߵķ�ʽ�������ķ�ʽ��ʹ����3�������ķ�ʽ
  1. ��һ������-ʵ�߳���
  2. ��϶����
  3. ��ʼ���루�����ڵ�һ�����߶��ϣ�û����ȫ��⣬������޸Ĵ�������۲����ɵ�pdf��⣩
  
**������������ĳ��û��Ʒ���**
1. curveTo()
2. rectangle()
3. fill()

## �ı���״̬
![�ھ���λ������ı�](https://developers.itextpdf.com/sites/default/files/C02F03.png)
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
* ʵ����ͼPDF����÷�����ʹ��һ��Paragraph��������with��ͬ�Ķ��뷽ʽ��center for title,left for body����֮����ЩParagraph������ӵ�Document�У�high-level������
* �����high-level��������ӹ����л��Զ����У����Զ���ҳ
* �����low-level��������Ҫ�Լ��ֶ�ȥ���ı��ָ��С��chunk

```
canvas.concatMartx(1,0,0,1,0,ps.getHeight());
canvas.beginText()
      .setFontAndSize(PdfFontFactory.createFont(FontConstants.COURIER_BOLD),14)
      .setLeading(14 * 1.2f)
      .moveText(70,-40);
```
* ������ϵͳ��ԭ���������Ͻ�
* beginText()����text����
* setFontAndSize()����������ֺţ����������е�canvas����ӵ�text�����������ֺ�
* setLeading()�����о�Ϊ�ֺŵ�1.2��
* moveText()���ù����ʼλ��
```
for(String s:text){
      // Add text and move to the next line
      canvas.newlineShowText(s);
}
canvas.endText();
```
* newlineShowText()�Ὣ����ƶ���֮ǰһ�е��±ߵ�16.2����λ���оࣩ��
* endText()�ر�text����

![adding skewed and colored text at absolute positions](https://developers.itextpdf.com/sites/default/files/C02F04.png)

```
canvas.rectangel(0,0,ps.getWidth(),ps.getHeight())
      .setColor(Color.BLACK,true)
      .fill();
```
* ����ʹ��setFillColor(Color.BLACK)��Ϊ���������ɫ����ѡ���˸�Ϊͨ�õ�setColor()������true or false��������Ϊfill����Ϊstroke��ɫ

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
* ��Ϊ����ʹ��newlineShowText()�����Բ���Ҫ����leading
* �������ֵ���ɫʱ��ʹ�õ���setColor(yellowColor,true)��������Ϊ���ֻ�ռ��·�������е����ݣ����ʹ��true
* canvas.setTextMatrix(fontSizeCoeff,0,angle,fontSizeCoeff/1.5f,xOffset+charXOffset,yOffset-lineSpacing).showText(String.valueOf(line.charAt(i)));a��d���������ţ�c��������б�ȣ�e��f������λ��

# ʹ����Ⱦ�����¼�������

## document��Ⱦ��
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
* offSet������ҳ�߾�
* columnWidth������ÿһ�еĿ�ȣ�ע��ÿһ��֮�����м���ģ�����Ϊ5����λ�����ܿ����ҳ���ȼ�ȥ������offSet��������5
* columnHeight������ÿһ�еĸ߶�
* Rectangle����new Rectangle(a,b,c,d)
  * a��ʾ������ʼ������
  * b��ʾ������ʼ������
  * c��ʾ���ο��
  * d��ʾ���θ߶�
* setRenderer()��һ��������document����Ⱦ���������������ݲ��ֶ�Ҫ�����������

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
  
## ʹ�ÿ���Ⱦ��

**Ĭ������£����Ԫ��û�б�����ɫ���߿�Ϊ��ɫ���߿��߿�Ϊ0.5����λ**
![a table with colored cells and rounded borders](https://developers.itextpdf.com/sites/default/files/C03F02_0.png)
```
PageSize ps = new PageSize(842,680);
```
* �����Զ���ҳ���С
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
* ���ռ���˿���ҳ��100%�Ŀ�ȣ��Ƿ������ҳ�߾ࣿ��

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
* setBorder()������Ĭ�ϵ�border

***SolidBorder��̳���Border�࣬ͬ�����໹��DashedBorder��DottedBorder��DoubleBorder�ȵȡ����iText��û���ṩ����Ҫ��border���࣬
����Լ̳�Border�࣬�������Ѿ��ṩ�Ľӿ�Ϊ��д����Զ����CellRenderer�ӿ�***

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
* ����һ���Զ����CellRenderer������RoundedCornersCellRenderer()
* setBorder(null)�����������Ϊ�յĻ�������������ص��ĵ�Ԫ��һ����itext���Ƶģ���һ������Ⱦ�����Ƶ�
* CellRenderer����BlockRenderer�������ʵ��

***BlockRenderere����Ա�����BlockElements������Paragraph��List����Щ��Ⱦ����������дdraw()�������Զ������Ⱦ����***

* getOccupiedAreaBBox()����һ��Rectangle���󣬶�������Ϊ���BlockElement����Ӧ�ı߽��
* ����drawContext�����ṩPdfCanvasʵ��
* ���������ʾ����ν�high-level��Cell����low-level������

***�������ߵ�ʱ����ҪһЩ��ѧ֪ʶ�������Ⲣ�����ӣ�����***

## �¼�������
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
* addEventHandler(PdfDocumentEvent.END_PAGE,new MyEventHandler());MyEventHandler�ǽӿ�IEventHandler��ʵ�֣�
����ӿ���ֻ��һ��������handleEvent()����ҳ�����ʱ����������ᴥ����ҳ�����������ҳ��Ĵ����;�ҳ��Ľ�����
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

***��ͬ��·������״��ͳһҳ���Ͽ����ص������ƵĹ�����ǣ�content stream�����ľ��Ȼ���������Ҫ����ӱ���ʱÿ�ζ��ܹ������е�
ҳ����ȫ��Ⱦ��ÿ��PdfPage���󶼱�����һ��content streams������Խ�������Ϊ������ʹ��getContentStream()��������ȡÿ��������content stream��
����ʹ��getFirstContentStream()��getLastContentStream()����ȡ��һ�������һ��content stream��Ҳ����ʹ��newContentStreamBefore()��
newContentStreamAfter()���������µ�content stream***

* ��handleEvent()�����У�����ʹ����PdfCanvas�Ĺ��췽�����������һ����ز�����
  * page.newContentStreamBefore()��Ϊ�˲�ʹҳüҳ�ź�ˮӡ������������
  * page.getResources()��ÿ��content stream����һЩ�ⲿ��Դ��أ����������ͼƬ��itext��Ҫ֪����Щ��Դ
  * pdfDoc

  
# �������н������ܵ�PDF����ǰ�����������Ǻ����漰��

## ���Annotations
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
***link ann���ᱻ��ӵ�content stream�У���Ϊann������content stream��һ���֡��������ᱻ��ӵ���Ӧҳ��Ķ�Ӧ����ϵ�У���仰�ľ������岻������ʱ���������
�ı�������Ϊ�ɵ���ģ�������ı��ı���content stream�е�״̬��***

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

***�����Ժ���½�����ר�ŵĽ̳���������Щע����صĴ���***

## ���������ı��

![an interactive form](https://developers.itextpdf.com/sites/default/files/C04F05.png)

  ��Ȼ���˾��ý�����񲿷ֺ���Ȥ�����������¼����������Ҿ������ٻ���ʱ��ȥ�����������
   
    1. ���������£�HTML�ı���Ϊ��̬������
    2. ��ʹ�ǽ̳���Ҳ��������������ʹ�����ּ���
    3. �������Ǳ�����Բ����漰�������������
    
  ���������ŵ��
    
    1. ���ڸ�ʽ�ϸ�ı������ּ����������
    2. ����Ϊ�ռ����ݵı���������Ϊģ��ʹ��ʱ��Ϊ����


# �����Ѵ��ڵ�PDF
***�ڵ������д�������ʽ��������ˣ�����������һ���ֹ��ڲ����Ѵ��ڱ���������Ҫ�ر�˵�����͵����µ���������ϵ������ڵ������н���˵��***
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
    // form.flattenFields(); // ����㲻ϣ���ն��޸���Щѡ��
    pdf.close();
```

## ���ann��content
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

## ���ҳü��ҳ�ź�ˮӡ
![UFO sightings report](https://developers.itextpdf.com/sites/default/files/C05F04_1.png)
![UFO sightings report with header, footer, and watermark](https://developers.itextpdf.com/sites/default/files/C05F05_0_0.png)
* �ڵ������У������ҳ�ŵ�ʱ�����ڲ���֪���ܵ�ҳ�����������޷��������"1 of 4"��ҳ�ţ��������Ѿ����ڵ�PDF�Ĺ����У��Ϳ��Ի�ȡ���ܵ�ҳ������

***�������µ�PDF��ʱ�򣬿�������ҳ������λ�÷���ռλ��������������е�PDF�����ݵ�ʱ����ʵ�ʵ�ҳ��������ռλ������ʵ������Ч���������ڵ�ǰ���Ž̳��в���˵��***

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

1. ���ҳü
2. ���ҳ�Ŵ�����
3. ���ҳ������
4. ���ˮӡ
  ***�����Ѿ�ʹ����PdfCanvasʱ��itext����������������document.showTextAligned(p,...)��д��***
  
## �ı�ҳ��Ĵ�С�ͷ���
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
***�������е�PDF�ļ���ҪһЩPDF��ص�֪ʶ���������������г��ֵ�MediaBox***


# �����Ѿ����ڵ�PDF

## ���ţ�ƴ�Ӻ�ƽ��
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
* ��������Ч���ǽ���ͼ���Ų�ͬ�����ӵ�PDF��

## ��װPDF
### �ϲ�PDF
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
**������PDF�ĵ��ϲ���һ��PDF�ĵ�**

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
**��ѡ����ҳ��ӵ��ϲ��ĵ���**

### ��ҳ����ӵ�PDF��
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
* ����һ�������

>>> TreeMap��һ�������Map���ڹ��췽���п����бȽ������������ն���ıȽϹ�����������Map�е�Ԫ�أ�û�в����������ʹ�õľ���Ĭ�ϵıȽ�����
������PDF����ᷢ��Ŀ¼�ǰ�����ĸ˳�����еġ�

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
1. �����������С�������Ϣ����PdfDocument��
2. ����֮ǰ��TreeMap��
3. �����Ӧҳ������ݲ����Ƶ�Ŀ��PDF�С�
4. TextԪ�����ҳ�룬ҳ���һ����Ϊ��һҳ���������ڼ���ҳ���С�
5. ����text�ı�����ɫΪColor.WHITE���⽫�����һ����Text��ͬ���Ĳ�͸���İ�ɫ���Ρ�����������ԭ�е�ҳ�롣
6. ��text���õ���ǰҳ��Ĺ̶�λ�á�λ�õĵ�����ΪX=549��Y=742�����Ϊ100��λ��
7. ����һ����������Ŀ���key
8. ����һ��PdfArray����������Ŀ��������Ϣ
   1. ��ص�Page����
   2. ����X��Y���������
   3. X����
   4. Y����
   5. ����
9. Ŀ��key��Ŀ��ҳ����Ϣ����
10. ���Ŀ¼����
11. Ϊ�������action��ʵ�ֵ����ת����Ӧ��ҳ�棩

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

***�ϱߵ�������ʵ��Ӧ����һ�㲻����֡�����TOCֻ��һ��ҳ����ɣ���document����Ӹ�����У����ᷢ��һ����ֵ�����
�ı����ݲ�����һҳ�Ĵ�С��������ӵڶ���ҳ�档���ǵڶ���ҳ���ֲ�����һ���µ�ҳ�棬�����ڵ�һ��ҳ��ѭ��������ݡ�Ҳ����˵��
��һҳ֮ǰд�����ݽ��ᱻ���ǡ���������ǿ��Խ���ģ��������Ľ̳��л��ᵽ��***

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

  ��Щ��չ���ܱ���Ҫ�ڻ����ҵ���֤�����ʹ��,��Ϊ�Ǳ�Դ��,��Ҳ����maven�ֿ���
  
  **��ȡ���֤����**
  ```
   <dependency>
        <groupId>com.itextpdf</groupId>
        <artifactId>itext-licensekey</artifactId>
        <version>2.0.4</version>
    </dependency>
  ```
  
  **ʹ����չ����**
  ```
    <repositories>
     <repository>
          <id>itext</id>
          <name>iText Repository - releases</name>
          <url>https://repo.itextsupport.com/releases</url>
      </repository>
    </repositories>
  ```




































