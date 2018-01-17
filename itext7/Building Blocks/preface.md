# 前言
**重点在high-level blocks。包括Paragraph，List，Image，Table**
![Overview of the interfaces](https://developers.itextpdf.com/sites/default/files/interfaces%20with%20methods.png)
* 最高级别的接口是IPropertyContainer：
      
      这个接口定义了属性的set，get和delete
    
      它有两个子接口：
      1. IElement
      2. IRenderer
* IElement接口将被Text，Paragraph和Table等对象实现，这些对象将直接或间接的添加到document中。

      它有两个子接口：
      1. ILeafElement：它是由哪些不能够包含任何其他对象的对象实现的——Text，Image
       
         可以将Text或者Image添加到Paragraph中，但不能够向Text或者Image中添加任何对象。
      2. ILargeElement：实现这个接口的对象可以在没有完成对象所有元素之前，将对象添加到document中的——比如Table
         
         在没有完成表格的Cell之前，你可以先将表格放置好，最后在添加Cell中的内容，这样做从理论上来讲是可以节省内存的使用的。
    
* IRenderer接口将被TextRenderer，ParagraphRenderer和TableRenderer等对象实现，这些对象是itext内部使用的，如果我们想对它们做出调整，可以子类化它们。

![Implementations of the IPropertyContainer interface](https://developers.itextpdf.com/sites/default/files/IPropertyContainers.png)
* IPropertyContainer接口是由抽象类ElementPropertyContainer实现的，这个类有3个子类
  1. Style：是所有style属性的容器，比如margins，padding和rotation。它从ElementPropertyContainer类中继承了widths,heights,colors，borders和alignments
  2. RootElement：定义了添加内容的方法，也么是add()，要么是showTextAligned()。Document对象会将内容添加到页面上。Canvas对象是没有页面概念的。
  它是high-level布局API和low-level核心API的桥梁。
 
 
**很遗憾，接下来的图都很不清晰，就不贴了**
  
