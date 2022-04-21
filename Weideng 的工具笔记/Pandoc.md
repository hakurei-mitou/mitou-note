# Pandoc

多功能的文档转换工具。

通常用于将 Markdown 转换为其它格式。

## 常见输入输出格式

| 格式                   | 参数     |
| ---------------------- | -------- |
| CSV 表格               | csv      |
| Word 文档              | docx     |
| EPUB 电子书            | epub     |
| HTML 网页              | html     |
| Markdown 文档          | markdown |
| PDF 文档（仅支持输出） | pdf      |
| PPt 文档（仅支持输出） | pptx     |
| JSON 数据              | json     |

## 基本使用

```shell
pandoc -f 输入格式 -t 输出格式 -o 输出文件名 输入文件
```

- 输出文件名

	文件完整名称（要带扩展名）。

- 输入文件

	可以是任何形式呈现的文件，本地文件、网络文件（使用 URL）等。

## 导出 Word

### 概念注意

当使用 Markdown 写论文稿（Word）时，注意二者的标题使用差别。

- 书写 Markdown

```
h1
h2
h3
h4
...
```

- 书写论文 word

```
			title
h1
h2
h3
h4
...
```

### 模板

- 指定模板

```shell
pandoc -f 输入格式 -t 输出格式 -o 输出文件名 输入文件 --reference-doc=FileName（等号不要空格）
```

- 查看默认模板

```shell
pandoc --print-default-data-file reference.docx > custom-reference.docx   # 重定向到 docx 文件
```

可以修改输出的模板文件，然后在导出时指定该文件作为模板，也可用其它文件作为模板。

### 可能错误

- 编码错误

	当文件不是 UTF-8 编码时，Pandoc 可能会报错，可以使用 iconv 工具进行转码。（Linux 中一般会默认安装 iconv，可利用 LIunx 完成转码）

- 图片路径错误

	相对路径要使用 `.\` 。
	
- 工作目录

  为保证相对路径的图片正常使用，工作目录应与 md 文件目录一致。

