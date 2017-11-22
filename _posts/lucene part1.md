---
layout: post
title: 全文检索 Part1
category: Lucene
tags: [Lucene]
---

# part 1

## INC

JDBC
```
数据库采集出来放到索引目录中(创建索引) 数据采集
创建文档对象 
创建域对象
与对象添加到文档对象
文档对象添加到集合
文档和索引 写入到索引库中

(指定索引目录  指定分词器)


分词器
查询器
核心

```
```
Solr（http://lucene.apache.org/solr） ，solr是apache的一个子项目，支持从关系数据库、xml文档中提取原始数据。
	Nutch（http://lucene.apache.org/nutch）, Nutch是apache的一个子项目，包括大规模爬虫工具，能够抓取和分辨web网站数据。
	jsoup（http://jsoup.org/ ），jsoup 是一款Java 的HTML解析器，可直接解析某个URL地址、HTML文本内容。它提供了一套非常省力的API，可通过DOM，CSS以及类似于jQuery的操作方法来取出和操作数据。



```




## 中文分词器
IK分词  message分词

扩展词典   禁用词典
配置文件 两个词典


## 查询





## 创建索引
```
public class IndexManager {

    @Test
    public void testIndexDocumentAdd() throws IOException {
        //采集数据
        BookDao bookDao = new BookDao();
        List<Book> BookList = bookDao.findAllBooks();

        List<Document> documents = new ArrayList<>();


        for (Book b: BookList
             ) {
            //创建文档对象
            Document document = new Document();
            //创建域对象
            //参数1 域名,
            //参数2  域值
            //参数3 是否存储到索引哭里面 如果不存储 查询的时候就查询不到
            Field idfield = new TextField("id",String.valueOf(b.getId()), Field.Store.YES);
            Field nameField = new TextField("name",b.getName(), Field.Store.YES);
            Field priceField = new TextField("price",String.valueOf(b.getPrice()), Field.Store.YES);
            Field picField = new TextField("pic",b.getPic(), Field.Store.YES);
            Field descField = new TextField("desc",b.getDesc(), Field.Store.NO);

            //将域添加到文档对象里面
            document.add(idfield);
            document.add(nameField);
            document.add(priceField);
            document.add(picField);
            document.add(descField);
            documents.add(document);
        }
        Directory directory = FSDirectory.open(new File("F:\\myindex"));

        Analyzer analyzer = new IKAnalyzer();

        IndexWriterConfig indexWriterConfig = new IndexWriterConfig(Version.LUCENE_4_10_3,analyzer);

        //将文档和索引写入到索引哭中
        IndexWriter indexWriter = new IndexWriter(directory,indexWriterConfig);

        //循环将文档写到索引库中
        for (Document document:documents
             ) {

            indexWriter.addDocument(document);
        }
        //提交索引和文档到磁盘
        indexWriter.commit();
        indexWriter.close();
    }

    /**
     * 测试中文分词 
     */
    @Test
    public void testIK() throws IOException {
        String text = "周杰伦爱上蔡依林";
        Analyzer analyzer = new IKAnalyzer();

        TokenStream tokenStream = analyzer.tokenStream("", text);
        CharTermAttribute charTermAttribute = tokenStream.addAttribute(CharTermAttribute.class);
        tokenStream.reset();

        while(tokenStream.incrementToken()){
            System.out.println(charTermAttribute);
        }

    }
}
```

## lucene的field域

不能灵活的控制是否分词和索引
<br>
每一个域类型都已经规定了是否要分词和索引

<br>
#### TextField : 文本(流)域类型
```
必须要分词
		必须要索引
		存储可选
```
<br>
#### 数字域
```
必须要分词(lucene底层对数字域的分词是一种特殊的分词方法,是为了更快的排序和筛选) > < 二进制 等等
		必须要索引(排序和筛选)
		存储可选
```
#### StringField(文本域)
```
必须不分词
		必须索引
		存储可选
```

#### StoreField(存储域)
```
		必须不分词
		必须不索引
		必须存储
```

## lucene的维护
```
	lucene底层其实没有修改索引和文档功能,所谓的修改,其实是先删除原文档,再添加新文档
```

## 查询

TermQuery词项查询<br>
NumericRangeQuery，数字范围<br>
BooleanQuery，布尔查询组合条件<br>

MultiFieldQueryParse对多个域查询。<br>

## 相关度
```
相关度排序

	Term Frequency (tf)：一个词在一片文档中出现了多少次,出现的次数越多,匹配度越高,排序就会越靠前
		
	  Document Frequency (df)：一个词在多少文档中出现了,出现的越多,这个词的权重就越低
```


## Solr
特点: 可以直接部署到web服务器下, 对外提供对索引库增删改查的api服务,
<br>
项目中假如solrJ客户端, 使用solrJ客户端. 可以实现对solr服务的调用,实现对索引库的增删改查 

## 目录结构

 
```
contrib: solr的扩展jar包
	dist: solr项目的war包, solrJ客户端
	example\solr: 一个solr标准的索引库配置
	example\lib\ext: 是solr项目部署的时候,还有solrj客户端 需要依赖的日志jar包
```
## Solr环境搭建

```
tomcat 
solr的war包,解压,然后拷贝到tomcat的webapps下
example\lib\ext日志jar包拷贝到solr项目的lib里面
example\solr拷贝出来,改名solrHome
solr项目的web.xml里面配置solrHome的路径
重启tomcat,访问http://localhost/solr
```

创建时  和 查询时  进行分词 

## solrJ + 依赖 + 日志



## Solr的schma.xml的介绍
```
<br>
	field标签: 配置自定义的域名
	dynamicField标签:动态域,我们没有配置的域名,可以使用动态域进行匹配
	uniqueKey标签: 主键域,配置主键域
	copyField标签: 搜索目标域,就像相当于在多个源域中搜索
	fieldType标签: 可以使用这个标签配置域类型,域类型里面可以配置自定义分词器

```


## 配置中文分词器
	a: 将ik分词器的jar包拷贝到solr项目lib中,ik分词器的配置文件拷贝到solr项目的WEB-INFO\classes文件夹内
	b: 在schema.xml里面配置域类型,并配置ik分词器
	c: 配置一个filed域,引用配置的域类型
	d: 重启tomcat,在web客户端里面就可以看到配置的域名
	
	开发测试环境(就是测试人员使用的服务器)
	UAT环境(用户测试环境)
	生产环境

10 TO 100

## 搜索

1jar  solrJ   扩展日志
2webxml