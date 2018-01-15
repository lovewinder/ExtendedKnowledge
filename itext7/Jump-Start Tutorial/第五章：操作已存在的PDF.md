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







