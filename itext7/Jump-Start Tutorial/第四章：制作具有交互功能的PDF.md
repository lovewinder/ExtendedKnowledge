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







