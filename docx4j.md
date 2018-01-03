[TOC]
# Docx4j

## Docx4j说明

### Docx4j概述

  Docx4j是Java操作office2007+中的Word、Excel、PPT的开源项目，其主要针对WordXML同时也可以处理Excel和PPT，比POI要强大很多.
  
### Docx4j能做什么

1. 打开已存在的docx,pptx,xlsx
2. 创建新的docx,pptx,xlsx
3. 编程式地操作上面打开的文档

## Docx4j的使用

  Docx4j中,在内存中操作的word文档是"WordprocessingMLPackage"类型对象(本文一下简称包)
 
  在编辑一个word文档前,开发者需要选择:创建一个新的空白包,并逐一将需要的内容填充进去,或者打开一个已有的文档,并在里面添加/替换新的内容
  
  前者思路比较简单,比较适合简单文档的创建.但由于添加每条新内容时,都需要手动进行设置其各项参数(比如表格的行款,列宽,边框等),且添加修改复杂空间(公式,页眉页脚)的过程都比较繁琐,所以在创建格式复杂的文档时不是很建议.
  
  后者需要事先制作一个模板文档,添加不同的占位符和各种模板信息,在准备上比前者复杂,但也具有很多优点:
  
  * 可以简化细节参数的调整(不需要手动调整表格,段落的具体细节参数)从而将精力集中到文档内容上
  * 复杂的文档部分(如,公式/复选框等)可以直接从模板中读取,只需要在其基础上修改文字等内容部分,而避开了繁琐的创建操作等
  * 在创建格式复杂的文档时,这个方法相比前者可以精简大量代码
  
## Docx4j编程

### 基本元素操作

  * 创建包
  
  ```
  WordprocessingMLPackage wordMLPackage = WordprocessingMLPackage.createPackage();
  ```
  
  * 保存包
  
  ```
  wordMLPackage.save(new File("D://x.docx"));
  ```
  
  * 得到主段落,并且输出/带样式输出:
  
  ```
  MainDocumentPart documentPart = wordMLPackage.getMainDocumentPart();
  // documentPart.addParagraphOfText("Hello World!");
  documentPart.addStyledParagraphOfText("Title","Hello World!");
  // documentPart.addStyledParagraphOfText("Subtitle","a subtitle");
  ```
  
  * 创建表格并添加内容
  
  ```
  ObjectFactory factory = Context.getWmlObjectFactory();
  Tbl table = factory.createTbl();
  Tr tableRow = factory.createTr();
  Tc tableCell = factory.createTc();
  tableCell.getContent().add(documentPart.createParagraphOfText("Field 1"));
  tableRow.getContent().add(tableCell);
  table.getContent.add(tableRow);
  documentPart.addObject(table);
  ```
  
  先创建一个工厂,创建表格,再创建行和单元格(tableCell),在单元格里添加你想要的内容
  
  * 编辑表格样式
  
  ```
  table.setTblPr(new TblPr());  
  CTBorder border = new CTBorder();  
  border.setColor("auto");  
  border.setSz(new BigInteger("4")); 
  TblBorders borders = new TblBorders();  
  borders.setBottom(border);  
  borders.setLeft(border);  
  borders.setInsideV(border);  
  table.getTblPr().setTblBorders(borders);  
  ```
  
  先创建table样式对象,再用CTBorder对象规定样式规范,用TblBorders对象将样式规范应用进去
  
  * 创建 段落/运行块/运行块属性/文本 对象
  
  ```
  ObjectFactory factory=Context.getWmlObjectFactory();  
  P paragraph = factory.createP();  
  Text text = factory.createText();  
  text.setValue(content);  
  R run = factory.createR();  
  run.getContent().add(text);  
  paragraph.getContent().add(run);  
  RPr runProperties = factory.createRPr(); 
  run.setRPr(runProperties);  
  tableCell.getContent().add(paragraph);  
  ```
  
  P是一个段落,Text是文本的值对象;
  
  R是一个运行块,负责将多个属性相同的Text对象统一操作;
  
  RPr是运行块的属性,可以对R对象进行操作
  
  ** Tc tableCell>P paragraph>R run>Text text **
  
  * 加粗字体和调整字体大小
  
  ```
  HpsMeasure size = new HpsMeasure();  
  size.setVal(new BigInteger("40"));  
  runProperties.setSz(size);  
  runProperties.setSzCs(size);  
  BooleanDefaultTrue b = new BooleanDefaultTrue();  
  b.setVal(true);  
  runProperties.setB(b);  
  ```
  
  思路是创建个字的对象,设置对象的值为自己想要的情况,再用RPr的对象来set相应的属性(setVal中的值最后会被实现一半)
  
  * 纵向合并单元格
  
  ```
  Tc tableCell = factory.createTc();  
  TcPr tableCellProperties = new TcPr();  
  VMerge merge = new VMerge();  
  merge.setVal("restart");  
  tableCellProperties.setVMerge(merge);  
  tableCell.setTcPr(tableCellProperties);  
  tableCell.getContent().add(wordMLPackage.getMainDocumentPart().createParagraphOfText(content));  
  row.getContent().add(tableCell);
  ```
  
  先创建单元格属性,再创建VMerge对象,如果将merge属性设为restart则重新开始新的单元格
  
  * 设置单元格宽度
  
  ```
  TcPr tableCellProperties = new TcPr();
  TblWidth tableWidth = new TblWidth();
  tableWidth.setW(BigInteger.valueOf("50"));
  tableCellProperties.setTcW(tableWidth);
  tableCell.setTcPr(tableCellProperties);
  ```
  
  先创建单元格属性对象,创建Tblwidth对象并且设置宽度,用单元格属性对象通过方法调用Tbwidth对象
  
  * 添加图片
  
  ```
  File file = new File("c:\\a.jpg");
  BinaryPartAbstractImage imagePart = BinaryPartAbstractImage.createImagePart(wordMLPackage, file);
  int docPrId = 1;
  int cNvPrId = 2;
  Inline inline = imagePart.createImageInline("Filename hint","Alternative text", docPrId, cNvPrId, false);
  ObjectFactory factory = new ObjectFactory();
  P paragraph = factory.createP();
  R run = factory.createR();
  paragraph.getContent().add(run);
  Drawing drawing = factory.createDrawing();
  run.getContent().add(drawing);
  drawing.getAnchorOrInline().add(inline);
  wordMLPackage.getMainDocumentPart().addObject(paragraph);
  ```
  
  打开文件,通过imagePart将图片读进去,现在图片被转换成二进制,为了能在文件内联中显示出图片,调用函数将图片存放在inline中,之后paragraph,run中可以用drawing读取inline
  
  * 加载读入docx文件
  
  ```
  WordprocessingMLPackage template = WordprocessingMLPackage.load(new File("D:\\a.docx"));
  ```
  
  * 获取文档中所有内容(方法)
  
  ```
  private static List<Object> getAllElementFromObject(Object obj, Class<?> toSearch) {
		List<Object> result = new ArrayList<Object>();
		if (obj instanceof JAXBElement)
			obj = ((JAXBElement<?>) obj).getValue();
		if (obj.getClass().equals(toSearch))
			result.add(obj);
		else if (obj instanceof ContentAccessor) {
			List<?> children = ((ContentAccessor) obj).getContent();
			for (Object child : children) {
				result.addAll(getAllElementFromObject(child, toSearch));
			}
		}
		return result;
	}
  ```
  
  
  