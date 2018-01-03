[TOC]
# Docx4j

## Docx4j˵��

### Docx4j����

  Docx4j��Java����office2007+�е�Word��Excel��PPT�Ŀ�Դ��Ŀ������Ҫ���WordXMLͬʱҲ���Դ���Excel��PPT����POIҪǿ��ܶ�.
  
### Docx4j����ʲô

1. ���Ѵ��ڵ�docx,pptx,xlsx
2. �����µ�docx,pptx,xlsx
3. ���ʽ�ز�������򿪵��ĵ�

## Docx4j��ʹ��

  Docx4j��,���ڴ��в�����word�ĵ���"WordprocessingMLPackage"���Ͷ���(����һ�¼�ư�)
 
  �ڱ༭һ��word�ĵ�ǰ,��������Ҫѡ��:����һ���µĿհװ�,����һ����Ҫ����������ȥ,���ߴ�һ�����е��ĵ�,�����������/�滻�µ�����
  
  ǰ��˼·�Ƚϼ�,�Ƚ��ʺϼ��ĵ��Ĵ���.���������ÿ��������ʱ,����Ҫ�ֶ�����������������(��������п�,�п�,�߿��),������޸ĸ��ӿռ�(��ʽ,ҳüҳ��)�Ĺ��̶��ȽϷ���,�����ڴ�����ʽ���ӵ��ĵ�ʱ���Ǻܽ���.
  
  ������Ҫ��������һ��ģ���ĵ�,��Ӳ�ͬ��ռλ���͸���ģ����Ϣ,��׼���ϱ�ǰ�߸���,��Ҳ���кܶ��ŵ�:
  
  * ���Լ�ϸ�ڲ����ĵ���(����Ҫ�ֶ��������,����ľ���ϸ�ڲ���)�Ӷ����������е��ĵ�������
  * ���ӵ��ĵ�����(��,��ʽ/��ѡ���)����ֱ�Ӵ�ģ���ж�ȡ,ֻ��Ҫ����������޸����ֵ����ݲ���,���ܿ��˷����Ĵ���������
  * �ڴ�����ʽ���ӵ��ĵ�ʱ,����������ǰ�߿��Ծ����������
  
## Docx4j���

### ����Ԫ�ز���

  * ������
  
  ```
  WordprocessingMLPackage wordMLPackage = WordprocessingMLPackage.createPackage();
  ```
  
  * �����
  
  ```
  wordMLPackage.save(new File("D://x.docx"));
  ```
  
  * �õ�������,�������/����ʽ���:
  
  ```
  MainDocumentPart documentPart = wordMLPackage.getMainDocumentPart();
  // documentPart.addParagraphOfText("Hello World!");
  documentPart.addStyledParagraphOfText("Title","Hello World!");
  // documentPart.addStyledParagraphOfText("Subtitle","a subtitle");
  ```
  
  * ��������������
  
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
  
  �ȴ���һ������,�������,�ٴ����к͵�Ԫ��(tableCell),�ڵ�Ԫ�����������Ҫ������
  
  * �༭�����ʽ
  
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
  
  �ȴ���table��ʽ����,����CTBorder����涨��ʽ�淶,��TblBorders������ʽ�淶Ӧ�ý�ȥ
  
  * ���� ����/���п�/���п�����/�ı� ����
  
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
  
  P��һ������,Text���ı���ֵ����;
  
  R��һ�����п�,���𽫶��������ͬ��Text����ͳһ����;
  
  RPr�����п������,���Զ�R������в���
  
  ** Tc tableCell>P paragraph>R run>Text text **
  
  * �Ӵ�����͵��������С
  
  ```
  HpsMeasure size = new HpsMeasure();  
  size.setVal(new BigInteger("40"));  
  runProperties.setSz(size);  
  runProperties.setSzCs(size);  
  BooleanDefaultTrue b = new BooleanDefaultTrue();  
  b.setVal(true);  
  runProperties.setB(b);  
  ```
  
  ˼·�Ǵ������ֵĶ���,���ö����ֵΪ�Լ���Ҫ�����,����RPr�Ķ�����set��Ӧ������(setVal�е�ֵ���ᱻʵ��һ��)
  
  * ����ϲ���Ԫ��
  
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
  
  �ȴ�����Ԫ������,�ٴ���VMerge����,�����merge������Ϊrestart�����¿�ʼ�µĵ�Ԫ��
  
  * ���õ�Ԫ����
  
  ```
  TcPr tableCellProperties = new TcPr();
  TblWidth tableWidth = new TblWidth();
  tableWidth.setW(BigInteger.valueOf("50"));
  tableCellProperties.setTcW(tableWidth);
  tableCell.setTcPr(tableCellProperties);
  ```
  
  �ȴ�����Ԫ�����Զ���,����Tblwidth���������ÿ��,�õ�Ԫ�����Զ���ͨ����������Tbwidth����
  
  * ���ͼƬ
  
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
  
  ���ļ�,ͨ��imagePart��ͼƬ����ȥ,����ͼƬ��ת���ɶ�����,Ϊ�������ļ���������ʾ��ͼƬ,���ú�����ͼƬ�����inline��,֮��paragraph,run�п�����drawing��ȡinline
  
  * ���ض���docx�ļ�
  
  ```
  WordprocessingMLPackage template = WordprocessingMLPackage.load(new File("D:\\a.docx"));
  ```
  
  * ��ȡ�ĵ�����������(����)
  
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
  
  
  