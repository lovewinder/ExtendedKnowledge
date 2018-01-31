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

  有些扩展功能必须要在获得商业许可证后才能使用,因为是闭源的,它也不在maven仓库中
  
  **获取许可证方法**
  ```
   <dependency>
        <groupId>com.itextpdf</groupId>
        <artifactId>itext-licensekey</artifactId>
        <version>2.0.4</version>
    </dependency>
  ```
  
  **使用扩展功能**
  ```
    <repositories>
     <repository>
          <id>itext</id>
          <name>iText Repository - releases</name>
          <url>https://repo.itextsupport.com/releases</url>
      </repository>
    </repositories>
  ```