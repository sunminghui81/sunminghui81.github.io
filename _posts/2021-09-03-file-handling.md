---
layout: post
keywords: file, text, bytes, unicode, csv
description: 收集文件处理相关的入门资料
title: "路径和文件处理参考资料"
categories: [python]
tags: [file, text, bytes, unicode, csv, json, pdf]
group: archive
icon: compass
---
{% include mathsyouth/setup %}


### Understand File Systems

1. [Working With Files in Python](https://realpython.com/working-with-files-in-python/)
1. [Python 3's pathlib Module: Taming the File System](https://realpython.com/python-pathlib/)
1. [No Really, Python's Pathlib is Great](https://rednafi.github.io/digressions/python/2020/04/13/python-pathlib.html)
1. [No really, pathlib is great](https://treyhunner.com/2019/01/no-really-pathlib-is-great/)
1. [Why pathlib.Path doesn't inherit from str in Python](https://snarky.ca/why-pathlib-path-doesn-t-inherit-from-str/)
1. [Reading and Writing Files in Python (Guide)](https://realpython.com/read-write-files-python/)
1. [Python: How to read and write files](https://thepythonguru.com/python-how-to-read-and-write-files/)


### 字符编解码

1. [字符串和编码](https://www.liaoxuefeng.com/wiki/1016959663602400/1017075323632896)
1. [全网最全面、全详细的编码、解码知识！！！](https://zhuanlan.zhihu.com/p/165989439)
1. [字符编码笔记：ASCII，Unicode 和 UTF-8](http://www.ruanyifeng.com/blog/2007/10/ascii_unicode_and_utf-8.html)


### Understand Unicode

1. [The Absolute Minimum Every Software Developer Absolutely, Positively Must Know About Unicode and Character Sets (No Excuses!)](https://www.joelonsoftware.com/2003/10/08/the-absolute-minimum-every-software-developer-absolutely-positively-must-know-about-unicode-and-character-sets-no-excuses/)
1. [Unicode strings in Python: A basic tutorial](http://pgbovine.net/unicode-python.htm)
1. [Unicode errors in Python 2](http://pgbovine.net/unicode-python-errors.htm)
1. [Unconfusing Unicode: What is Unicode?](https://regebro.wordpress.com/2011/03/23/unconfusing-unicode-what-is-unicode/)
1. [Unicode & Character Encodings in Python: A Painless Guide](https://realpython.com/python-encodings-guide/)
1. [Unicode HOWTO](https://docs.python.org/3/howto/unicode.html)
1. Text versus Bytes in Chapter 4 of "Fluent Python"
1. [Pragmatic Unicode](https://nedbatchelder.com/text/unipain.html)
1. [Programming with Unicode](https://unicodebook.readthedocs.io/index.html)
1. [UTF-8 编解码实现](https://taoshu.in/c-utf-8.html)
1. https://www.cl.cam.ac.uk/~mgk25/ucs/utf-8-history.txt
1. [Simple, fast C library for manipulating strings in the UTF-8 encoding](https://github.com/JeffBezanson/cutef8)


### Handle CSV files

1. [Python: How to read and write CSV files](https://thepythonguru.com/python-how-to-read-and-write-csv-files/)


### Handle JSON files

1. [Working With JSON Data in Python](https://realpython.com/python-json/)
1. [Choosing a faster JSON library for Python](https://pythonspeed.com/articles/faster-json-library/)
1. [jq](https://stedolan.github.io/jq/) is a lightweight and flexible command-line JSON processor.
1. [fx](https://github.com/antonmedv/fx) Command-line JSON processing tool.
1. [ijson](https://github.com/ICRAR/ijson) is an iterative JSON parser with standard Python iterator interfaces.


### Handler Excel Files

1. [pyexcel](https://github.com/pyexcel/pyexcel) is a simple API for reading, manipulating and writing data in csv, ods, xls, xlsx and xlsm files.
1. [Working with Excel Files in Python](http://www.python-excel.org/)
1. [XlsxWriter](https://github.com/jmcnamara/XlsxWriter) is a Python module for writing files in the Excel 2007+ XLSX file format.


### PDF 工具

1. [pdfminer.six](https://github.com/pdfminer/pdfminer.six) PDF转换TXT工具
2. https://github.com/claird/PyPDF4
3. https://github.com/tabulapdf/tabula
4. https://github.com/jstockwin/py-pdf-parser
5. https://github.com/kermitt2/grobid
6. [PDFBox](https://pdfbox.apache.org/) 其源码维护更加活跃，并且提供了下面的功能：
   * 文本的提取，即在PDF文件里面把文本内容提取出来；
   * 合并和拆分PDF文档，可以把几个PDF合并成一个PDF文件，也可以把一个PDF文件拆分成多个PDF文件；
   * PDF的校验，根据PDF/AISO 标准校验PDF文档；
   * 打印PDF，通过Java的API去打印PDF文件；
   * 把PDF转换成图片，把PDF文件转换成图片；
   * 创建PDF文件；
   * PDF签名

