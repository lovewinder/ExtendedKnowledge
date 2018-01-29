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

