# 常用功能小结

### 新增超链接

```
public void createHyperlink(WordprocessingMLPackage wordMLPackage, MainDocumentPart mainPart, ObjectFactory factory,
			P paragraph, String url, String value, String cnFontName, String enFontName, String fontSize)
			throws Exception {
		if (StringUtils.isBlank(enFontName)) {
			enFontName = "Times New Roman";
		}
		if (StringUtils.isBlank(cnFontName)) {
			cnFontName = "微软雅黑";
		}
		if (StringUtils.isBlank(fontSize)) {
			fontSize = "22";
		}
		org.docx4j.relationships.ObjectFactory reFactory = new org.docx4j.relationships.ObjectFactory();
		org.docx4j.relationships.Relationship rel = reFactory.createRelationship();
		rel.setType(Namespaces.HYPERLINK);
		rel.setTarget(url);
		rel.setTargetMode("External");
		mainPart.getRelationshipsPart().addRelationship(rel);
		StringBuffer sb = new StringBuffer();
		// addRelationship sets the rel's @Id
		sb.append("<w:hyperlink r:id=\"");
		sb.append(rel.getId());
		sb.append("\" xmlns:w=\"http://schemas.openxmlformats.org/wordprocessingml/2006/main\" ");
		sb.append("xmlns:r=\"http://schemas.openxmlformats.org/officeDocument/2006/relationships\" >");
		sb.append("<w:r><w:rPr><w:rStyle w:val=\"Hyperlink\" />");
		sb.append("<w:rFonts w:ascii=\"");
		sb.append(enFontName);
		sb.append("\" w:hAnsi=\"");
		sb.append(enFontName);
		sb.append("\" w:eastAsia=\"");
		sb.append(cnFontName);
		sb.append("\" w:hint=\"eastAsia\"/>");
		sb.append("<w:sz w:val=\"");
		sb.append(fontSize);
		sb.append("\"/><w:szCs w:val=\"");
		sb.append(fontSize);
		sb.append("\"/></w:rPr><w:t>");
		sb.append(value);
		sb.append("</w:t></w:r></w:hyperlink>");

		Hyperlink link = (Hyperlink) XmlUtils.unmarshalString(sb.toString());
		paragraph.getContent().add(link);
	}
```

### 保存文件

```
 public void saveWordPackage(WordprocessingMLPackage wordPackage, File file) throws Exception {
        wordPackage.save(file);
    }
```

### 新建文件

```
public WordprocessingMLPackage createWordprocessingMLPackage() throws Exception {
        return WordprocessingMLPackage.createPackage();
    }
```

### 加载文件模板

```
public WordprocessingMLPackage loadWordprocessingMLPackage(String filePath) throws Exception {
        WordprocessingMLPackage wordMLPackage = WordprocessingMLPackage.load(new java.io.File(filePath));
        return wordMLPackage;
    }
```

### 跨列合并

```
public void mergeCellsHorizontalByGridSpan(Tbl tbl, int row, int fromCell, int toCell) {
        if (row < 0 || fromCell < 0 || toCell < 0) {
            return;
        }
        List<Tr> trList = getTblAllTr(tbl);
        if (row > trList.size()) {
            return;
        }
        Tr tr = trList.get(row);
        List<Tc> tcList = getTrAllCell(tr);
        for (int cellIndex = Math.min(tcList.size() - 1, toCell); cellIndex >= fromCell; cellIndex--) {
            Tc tc = tcList.get(cellIndex);
            TcPr tcPr = getTcPr(tc);
            if (cellIndex == fromCell) {
                GridSpan gridSpan = tcPr.getGridSpan();
                if (gridSpan == null) {
                    gridSpan = new GridSpan();
                    tcPr.setGridSpan(gridSpan);
                }
                gridSpan.setVal(BigInteger.valueOf(Math.min(tcList.size() - 1, toCell) - fromCell + 1));
            } else {
                tr.getContent().remove(cellIndex);
            }
        }
    }
```

### 跨行合并

```
public void mergeCellsVertically(Tbl tbl, int col, int fromRow, int toRow) {
        if (col < 0 || fromRow < 0 || toRow < 0) {
            return;
        }
        for (int rowIndex = fromRow; rowIndex <= toRow; rowIndex++) {
            Tc tc = getTc(tbl, rowIndex, col);
            if (tc == null) {
                break;
            }
            TcPr tcPr = getTcPr(tc);
            VMerge vMerge = tcPr.getVMerge();
            if (vMerge == null) {
                vMerge = new VMerge();
                tcPr.setVMerge(vMerge);
            }
            if (rowIndex == fromRow) {
                vMerge.setVal("restart");
            } else {
                vMerge.setVal("continue");
            }
        }
    }
```

### 得到指定位置的单元格

```
public Tc getTc(Tbl tbl, int row, int cell) {
        if (row < 0 || cell < 0) {
            return null;
        }
        List<Tr> trList = getTblAllTr(tbl);
        if (row >= trList.size()) {
            return null;
        }
        List<Tc> tcList = getTrAllCell(trList.get(row));
        if (cell >= tcList.size()) {
            return null;
        }
        return tcList.get(cell);
    }
```

### 得到所有表格

```
 public List<Tbl> getAllTbl(WordprocessingMLPackage wordMLPackage) {
        MainDocumentPart mainDocPart = wordMLPackage.getMainDocumentPart();
        List<Object> objList = getAllElementFromObject(mainDocPart, Tbl.class);
        if (objList == null) {
            return null;
        }
        List<Tbl> tblList = new ArrayList<Tbl>();
        for (Object obj : objList) {
            if (obj instanceof Tbl) {
                Tbl tbl = (Tbl) obj;
                tblList.add(tbl);
            }
        }
        return tblList;
    }
```

### 删除指定位置的表格

```
public boolean removeTableByIndex(WordprocessingMLPackage wordMLPackage, int index) throws Exception {
        boolean flag = false;
        if (index < 0) {
            return flag;
        }
        List<Object> objList = wordMLPackage.getMainDocumentPart().getContent();
        if (objList == null) {
            return flag;
        }
        int k = -1;
        for (int i = 0, len = objList.size(); i < len; i++) {
            Object obj = XmlUtils.unwrap(objList.get(i));
            if (obj instanceof Tbl) {
                k++;
                if (k == index) {
                    wordMLPackage.getMainDocumentPart().getContent().remove(i);
                    flag = true;
                    break;
                }
            }
        }
        return flag;
    }
```

### 获取单元格内容,无分割符

```
 public String getTblContentStr(Tbl tbl) throws Exception {
        return getElementContent(tbl);
    }
```

### 获取表格内容

```
public List<String> getTblContentList(Tbl tbl) throws Exception {
        List<String> resultList = new ArrayList<String>();
        List<Tr> trList = getTblAllTr(tbl);
        for (Tr tr : trList) {
            StringBuffer sb = new StringBuffer();
            List<Tc> tcList = getTrAllCell(tr);
            for (Tc tc : tcList) {
                sb.append(getElementContent(tc) + ",");
            }
            resultList.add(sb.toString());
        }
        return resultList;
    }
```

### 设置表格总宽度

```
public void setTableWidth(Tbl tbl, String width) {
        if (StringUtils.isNotBlank(width)) {
            TblPr tblPr = getTblPr(tbl);
            TblWidth tblW = tblPr.getTblW();
            if (tblW == null) {
                tblW = new TblWidth();
                tblPr.setTblW(tblW);
            }
            tblW.setW(new BigInteger(width));
            tblW.setType("dxa");
        }
    }
```

### 创建表格(默认水平居中,垂直居中)

```
public Tbl createTable(WordprocessingMLPackage wordPackage, int rowNum, int colsNum) throws Exception {
        colsNum = Math.max(1, colsNum);
        rowNum = Math.max(1, rowNum);
        int widthTwips = getWritableWidth(wordPackage);
        int colWidth = widthTwips / colsNum;
        int[] widthArr = new int[colsNum];
        for (int i = 0; i < colsNum; i++) {
            widthArr[i] = colWidth;
        }
        return createTable(rowNum, colsNum, widthArr);
    }
```

### 表格增加边框(可以设置上下左右四个边框样式以及横竖水平线样式)

```
public void setTblBorders(TblPr tblPr, CTBorder topBorder, CTBorder rightBorder, CTBorder bottomBorder,
            CTBorder leftBorder, CTBorder hBorder, CTBorder vBorder) {
        TblBorders borders = tblPr.getTblBorders();
        if (borders == null) {
            borders = new TblBorders();
            tblPr.setTblBorders(borders);
        }
        if (topBorder != null) {
            borders.setTop(topBorder);
        }
        if (rightBorder != null) {
            borders.setRight(rightBorder);
        }
        if (bottomBorder != null) {
            borders.setBottom(bottomBorder);
        }
        if (leftBorder != null) {
            borders.setLeft(leftBorder);
        }
        if (hBorder != null) {
            borders.setInsideH(hBorder);
        }
        if (vBorder != null) {
            borders.setInsideV(vBorder);
        }
    }

```

### 设至表格水平对齐方式(仅对表格起作用,单元格不一定水平对齐)

```
public void setTblJcAlign(Tbl tbl, JcEnumeration jcType) {
        if (jcType != null) {
            TblPr tblPr = getTblPr(tbl);
            Jc jc = tblPr.getJc();
            if (jc == null) {
                jc = new Jc();
                tblPr.setJc(jc);
            }
            jc.setVal(jcType);
        }
    }
```

### 设置表格水平对齐方式(包括单元格),支队该方法前面方法产生的单元格起作用

```
public void setTblAllJcAlign(Tbl tbl, JcEnumeration jcType) {
        if (jcType != null) {
            setTblJcAlign(tbl, jcType);
            List<Tr> trList = getTblAllTr(tbl);
            for (Tr tr : trList) {
                List<Tc> tcList = getTrAllCell(tr);
                for (Tc tc : tcList) {
                    setTcJcAlign(tc, jcType);
                }
            }
        }
    }
```

### 设置表格垂直对齐方式(包括单元格),只对该方法前面产生的单元格起作用

```
public void setTblAllVAlign(Tbl tbl, STVerticalJc vAlignType) {
        if (vAlignType != null) {
            List<Tr> trList = getTblAllTr(tbl);
            for (Tr tr : trList) {
                List<Tc> tcList = getTrAllCell(tr);
                for (Tc tc : tcList) {
                    setTcVAlign(tc, vAlignType);
                }
            }
        }
    }
```

### 工具方法

1.
  
```
public String getElementContent(Object obj) throws Exception {
        StringWriter stringWriter = new StringWriter();
        TextUtils.extractText(obj, stringWriter);
        return stringWriter.toString();
    }
```

2.

```
 public String getElementContent(Object obj) throws Exception {
        StringWriter stringWriter = new StringWriter();
        TextUtils.extractText(obj, stringWriter);
        return stringWriter.toString();
    }
```

[更多内容](http://www.cnblogs.com/cxxjohnson/p/7867577.html)
[表格](http://www.cnblogs.com/cxxjohnson/p/7886275.html)
[图片](http://www.cnblogs.com/cxxjohnson/p/7899256.html)