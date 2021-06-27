---
title: 【数据分析】第五阶段：python（二）Python爬虫&自动化办公
date: 2021-06-21 17:10:26
tags:
- 数据分析
- python
categories:
- 学习笔记
description: 本模块为Python语言的实战训练，通过爬虫采集数据，并进行清洗处理。使用Python第三方库对word, excel, pdf文件进行自动化处理，提取有效数据。
---

# 文件操作

## open()函数

aifc.open(file, mode=None)

打开一个 AIFF 或 AIFF-C 文件并返回一个对象实例，该实例具有下方描述的方法。

参数 file 是文件名称字符串或 文件对象。

当打开文件用于读取时，mode 必须为 'r' 或 'rb'，当打开文件用于写入时，mode 必须为 'w' 或 'wb'。

如果该参数省略，则使用 file.mode 的值（如果有），否则使用 'rb'。

当文件用于写入时，文件对象应该支持 seek 操作，除非提前获知写入的采样总数，并使用 writeframesraw() 和 setnframes()。

open() 函数可以在 with 语句中使用。当 with 块执行完毕，将调用 close() 方法。

```python
# 不指定mode参数，默认是'r'模式
open('contacts.txt')
```

### 'w'模式--write重写

```python
# 'w'，write写模式，不存在的文件会新建；'r'模式不会创建不存在的文件
f = open('contacts.txt','w')
f.close()
# 项目中没有contacts.txt文件，运行代码后在项目路径下新建了一个contacts.txt文件
```

```python
# 'w'模式，.write()方法向文件写入内容
f = open('contacts.txt','w')
f.write('王健林 13688888888')
f.write('马云 13866666666')
f.close()

王健林 13688888888马云 13866666666
```

```python
# 写入时加\n换行符，让数据换行
f.write('王健林 13688888888\n')
f.write('马云 13866666666\n')

王健林 13688888888
马云 13866666666
```

```python
# 'w'模式，清空文件内容，重写
# ./contacts.txt
王健林 13688888888
马云 13866666666

f = open('contacts.txt','w')
f.write('马化腾 13966666666\n')
f.close()

# ./contacts.txt
马化腾 13966666666
```

### 'r'模式--read只读

```python
# 'r'模式，.read()方法读取文件内容

f = open('contacts.txt','r')
content = f.read()
print(content)
f.close()

王健林 13688888888
马云 13866666666
```

### 'a'模式--append追加

```python
# 'a'模式，表示append模式，向文件末尾追加内容
# ./contacts.txt
王健林 13688888888
马云 13866666666

f = open('contacts.txt','a')
f.write('马化腾 13966666666\n')
f.close()

# ./contacts.txt
王健林 13688888888
马云 13866666666
马化腾 13966666666
```

### 'b'模式--binary二进制文件（图片等）

```python
# 'rb'可以省略写为'b'，'wb'不能省略
f = open('data_files/city_sale.png','b')
f2 = open('data_files/city_sale2.png','wb')
f2.write(f.read())
f.close()
f2.close()

'''
项目中有city_sale.png图片文件，路径为：项目路径/data_files/city_sale.png
没有city_sale2.png文件，f2新建了一个名为city_sale2.png的文件
f读取了已有图片的内容，写入到f2中
完成复制图片的操作
'''
```

## 异常处理

```python
try:
  '代码'
except Exception:
  '代码出现异常后执行'
finally: # finally部分可以不写
  '不管是否异常，这里都会执行'
```

Exception是常规异常的基类

```python
# 通过 Exception as 变量名 的写法，把异常赋给一个变量，打印出异常信息
try:
    1 + '1'
except Exception as e:
    errorPath = str(e.__traceback__.tb_frame.f_globals["__file__"]) # 错误文件
    errorLine = str(e.__traceback__.tb_lineno) # 错误行数
    errorInfo = str(e) # 错误信息
    print("\033[1;31m" + errorPath + "\033[0m") # 设置print字体颜色
    print("\033[1;31m" + errorLine + "\033[0m")
    print("\033[1;31m" + errorInfo + "\033[0m")
finally:
    print('over')
    
'''
/Users/ethan/PycharmProjects/pythonProject/exceptions.py
3
unsupported operand type(s) for +: 'int' and 'str'
over
'''
```

1、str(e)

返回字符串类型，只给出异常信息，不包括异常信息的类型，如1/0的异常信息

'integer division or modulo by zero'

2、repr(e)

给出较全的异常信息，包括异常信息的类型，如1/0的异常信息

"ZeroDivisionError('integer division or modulo by zero',)"

3、e.message

获得的信息同str(e)

4、采用traceback模块

　　需要导入traceback模块，此时获取的信息最全，与python命令行运行程序出现错误信息一致。使用traceback.print_exc()打印异常信息到标准错误，就像没有获取一样，或者使用traceback.format_exc()将同样的输出获取为字符串。你可以向这些函数传递各种各样的参数来限制输出，或者重新打印到像文件类型的对象。

```python
# traceback模块打印的错误信息和原生的一致
import traceback

try:
    1 + '1'
except Exception:
    traceback.print_exc()
finally:
    print('over')
    
'''
over
Traceback (most recent call last):
  File "/Users/ethan/PycharmProjects/pythonProject/exceptions.py", line 5, in <module>
    1 + '1'
TypeError: unsupported operand type(s) for +: 'int' and 'str'
'''
```

## with自动close

with写法，自动close文件，不需要手写close()

```python
with open('contacts.txt','r') as f:
  print(f.read())
  
# 等于

f = open('contacts.txt','r')
print(f.read())
f.close()
```

## CSV文件处理

```python
# data_files/example1.csv

日期,订单号,产品ID,产品类目,数量(斤）,单价,折扣,销售额
2020/4/1,2020040101,PID_0019,"芒,果",3,7.3,0.82,17.92
2020/4/1,2020040101,PID_0011,葡萄,1,8,0.64,5.12
2020/4/1,2020040101,PID_0004,香蕉,4,6.9,0.82,22.65
2020/4/1,2020040101,PID_0018,荔枝,1,3.4,0.73,2.47
2020/4/1,2020040102,PID_0019,芒果,2,3.6,0.64,4.6
2020/4/1,2020040102,PID_0001,荔枝,3,4.7,0.62,8.69
2020/4/1,2020040102,PID_0016,葡萄,2,4.6,0.84,7.65
2020/4/1,2020040102,PID_0018,荔枝,2,5.1,0.81,8.29
2020/4/1,2020040103,PID_0012,荔枝,4,11,0.92,40.6
2020/4/1,2020040103,PID_0011,葡萄,2,9.1,0.85,15.39
```

### 读取csv文件内容

#### readlines()

从输入流读取所有行并将其作为一个行列表返回。

```python
with open('data_files/example1.csv','r') as f:
  # f.readlines()返回一个列表，每个元素是文件中的每一行
  # print(f.readlines())
  # [
  #		'\ufeff日期,订单号,产品ID,产品类目,数量(斤）,单价,折扣,销售额\n', 
  #		'2020/4/1,2020040101,PID_0019,"芒,果",3,7.3,0.82,17.92\n',
  #		...]
    for line in f.readlines():
        print(line)
```

#### csv模块读取csv文件

通常使用csv模块处理csv文件

##### csv.reader()

返回一个 reader 对象，该对象将逐行遍历 *csvfile*。

csv 文件的每一行都读取为一个由字符串组成的列表。

```python
import csv

with open('data_files/example1.csv', 'r') as f:
    reader = csv.reader(f) # 返回reader对象
    for row in reader: # 每个row是一个列表
      # print(row)
      # ['\ufeff日期', '订单号', '产品ID', '产品类目', '数量(斤）', '单价', '折扣', '销售额']
			# ['2020/4/1', '2020040101', 'PID_0019', '芒,果', '3', '7.3', '0.82', '17.92']
        for col in row:
            print(col, end='\t')
        print()
```

##### csv.DictReader()

创建一个对象，该对象在操作上类似于常规 reader，但是将每行中的信息映射到一个 [`dict`](https://docs.python.org/zh-cn/3/library/stdtypes.html#dict)，该 dict 的键由 *fieldnames* 可选参数给出。

*fieldnames* 参数是一个 [sequence](https://docs.python.org/zh-cn/3/glossary.html#term-sequence)。如果省略 *fieldnames*，则文件 *f* 第一行中的值将用作字段名。无论字段名是如何确定的，字典都将保留其原始顺序。

```python
import csv

with open('data_files/example1.csv', 'r') as f:
    reader = csv.DictReader(f)
    for row in reader: # 每个row是一个字典
        # print(row)
        # {'\ufeff日期': '2020/4/1', '订单号': '2020040101', 
        #		'产品ID': 'PID_0019', '产品类目': '芒,果', '数量(斤）': '3', 
        #		'单价': '7.3', '折扣': '0.82', '销售额': '17.92'}
        #		...
        print(row['订单号'],row['销售额'])
```

### csv文件写入内容

#### csv.writer()

返回一个 writer 对象，该对象负责将用户的数据在给定的文件类对象上转换为带分隔符的字符串。

```python
import csv

sales = (
  # 姓名	一月业绩 二月业绩 三月业绩
    ("Peter", (78, 70, 65)),
    ("John", (88, 80, 85)),
    ("Tony", (90, 99, 95)),
    ("Henry", (80, 70, 55)),
    ("Mike", (95, 90, 95)),
)

with open('data_files/sales.csv', 'w') as f:
    writer = csv.writer(f)
    # 写个表头
    writer.writerow(['name', 'Jan', 'Feb', 'Mar'])
    for name, qa in sales:
      # writer.wirterow([name, qa[0], qa[1], qa[2]])
      # 用*解压符，*qa = qa[0], qa[1], qa[2],...
        writer.writerow([name, *qa])
```

#### csv.DictWriter()

创建一个对象，该对象在操作上类似常规 writer，但会将字典映射到输出行。 *fieldnames* 参数是由键组成的 [`序列`](https://docs.python.org/zh-cn/3/library/collections.abc.html#module-collections.abc)，它指定字典中值的顺序，这些值会按指定顺序传递给 `writerow()` 方法并写入文件 *f*。

```python
import csv
import pprint # 格式化打印输出模块

sales = (
    ("Peter", (78, 70, 65)),
    ("John", (88, 80, 85)),
    ("Tony", (90, 99, 95)),
    ("Henry", (80, 70, 55)),
    ("Mike", (95, 90, 95)),
)

# 将数据整理成一个列表，列表每一行代表数据的每一行，每一行是一个字典
data = [{'name': name, 'amount': sum(qa)} for name, qa in sales]

pprint.pprint(data) # 格式化打印输出
'''
 [{'amount': 213, 'name': 'Peter'},
 {'amount': 253, 'name': 'John'},
 {'amount': 284, 'name': 'Tony'},
 {'amount': 205, 'name': 'Henry'},
 {'amount': 280, 'name': 'Mike'}]
'''

with open('data_files/sales.csv', 'w') as f:
    fieldNames = ['name', 'amount']
    # DictWriter()方法必须指定列名，列名和数据字典的key一致
    writer = csv.DictWriter(f, fieldNames)
    # writerheader()写列名
    writer.writeheader()
    for row in data:
        writer.writerow(row)
```

## pip--python第三方库管理工具

python自带pip。相当于node自带的npm。

python3的pip命令是`pip3`。

官网：[https://pypi.org/](https://pypi.org/)

### 列出已安装的包

```
pip freeze` or `pip list
```

### 导出requirements.txt

```
pip freeze > <目录>/requirements.txt
```

### 安装包

#### 在线安装

```
pip install <包名>` 或 `pip install -r requirements.txt
```

*通过使用== >= <= > <来指定版本，不写则安装最新版*

requirements.txt内容格式为：

```
APScheduler==2.1.2
Django==1.5.4
MySQL-Connector-Python==2.0.1
MySQL-python==1.2.3
PIL==1.1.7
South==1.0.2
django-grappelli==2.6.3
django-pagination==1.0.7
```

#### 安装本地安装包

```
pip install <目录>/<文件名>` 或 `pip install --use-wheel --no-index --find-links=wheelhouse/ <包名>
```

*<包名>前有空格*

可简写为

```
pip install --no-index -f=<目录>/ <包名>
```

#### 卸载包

```
pip uninstall <包名>` 或 `pip uninstall -r requirements.txt
```

#### 升级包

```
pip install -U <包名>
```

或：pip install <包名> --upgrade

#### 升级pip

```
pip install -U pip
```

### 显示包所在的目录

```
pip show -f <包名>
```

macOS包默认安装路径：` /usr/local/lib/python3.9/site-packages`

### 搜索包

```
pip search <搜索关键字>
```

### 查询可升级的包

```
pip list -o
```

### 下载包而不安装

```
pip install <包名> -d <目录>` 或 `pip install -d <目录> -r requirements.txt
```

### 打包

```
pip wheel <包名>
```

### 更换国内pypi镜像

#### 国内pypi镜像

- 阿里云 http://mirrors.aliyun.com/pypi/simple/ 
- 中国科技大学 https://pypi.mirrors.ustc.edu.cn/simple/ 
- 豆瓣(douban) http://pypi.douban.com/simple/
- 清华大学 https://pypi.tuna.tsinghua.edu.cn/simple/ 
- 中国科学技术大学 http://pypi.mirrors.ustc.edu.cn/simple/

#### 指定单次安装源

```
pip install <包名> -i https://mirrors.aliyun.com/pypi/simple [--trusted-host mirrors.aliyun.com]
```

#### 指定全局安装源

在unix和macos，配置文件为：$HOME/.pip/pip.conf
在windows上，配置文件为：%HOME%\pip\pip.ini

```
[global]
index-url = 源地址
trusted-host = 源域名
```

# 办公自动化

## 处理excel

常用的处理excel的第三方包：

- xlrd：读取excel
- xlwt：写入excel
- xlutils：操作excel工具，复制、分割、筛选等

### excel读取

```python
import xlrd

# open_workbook()打开工作簿，返回工作簿对象
wb = xlrd.open_workbook('data_files/test.xls')

# nsheets sheets数量
print("sheet数量：", wb.nsheets)

# sheets() 返回一个列表，由每个sheet对象组成
sheets = wb.sheets()
print(sheets[0])

# sheet_names() 返回所有sheet名的列表
print(wb.sheet_names())

# sheet_by_index() 通过索引返回指定的sheet对象
sh = wb.sheet_by_index(0)
print(sh.name)
# sheet_by_name() 通过sheet名返回指定的sheet对象
sh = wb.sheet_by_name('fruits')
print(sh.name)

# 取整行、整列
print("sheet %s 有 %s 行 %s 列" % (sh.name, sh.nrows, sh.ncols))
print("第一行的值：", sh.row_values(0))
print("第二列的值：", sh.col_values(1))

# 单元格操作
cell = sh.cell(0, 1)
print('第一行第二列单元格的值：', cell.value)
# 0 空置：1 字符串：2 数值；3 日期
print('第一行第二列单元格值的类型', cell.ctype)
print('第3行第4列的值：', sh.cell_value(2, 3))
print('最后一列最后一行的值', sh.cell_value(-1, -1))

# 日期处理
cell = sh.cell(1, 0) # excel中是2020/04/01，cell.value返回的是43922.0
if cell.ctype == 3:
    value = xlrd.xldate_as_datetime(cell.value, 0)
```

**⚠️读取Excel注意**：

- 日期型数据返回的是距离1900年1月1日的天数，要用`xlrd.xldate_as_datetime()`转换成datetime对象。

### excel写入

```python
import xlwt

wb = xlwt.Workbook()
sh = wb.add_sheet('第一季度', cell_overwrite_ok=True)
# wb.add_sheet('第二季度')

sh.write(0, 0, '姓名')
sh.write(0, 1, '1月')
sh.write(0, 2, '2月')
sh.write(0, 3, '3月')

wb.save('data_files/test_write.xls')
```

## 处理word

### word写入

安装python-docx包

```python
from docx import Document
from docx.enum.text import WD_PARAGRAPH_ALIGNMENT
from docx.shared import Mm, Inches, Pt, RGBColor

doc = Document()

# 参数0代表标题的级别，0表示最大的1级标题
title = doc.add_heading("每日运营数据分析报告", 0)
# 设置标题水平剧中
title.paragraph_format.alignment = WD_PARAGRAPH_ALIGNMENT.CENTER

# 增加段落
doc.add_paragraph("此报告为python脚本每日自动生成，共分为三部分")
doc.add_paragraph("数据汇总", style="List Number")
doc.add_paragraph("线上数据", style="List Number")
doc.add_paragraph("线下数据", style="List Number")

doc.add_heading("数据汇总", 1)
online_sale = 292932
shop_sale = 192837
pg = doc.add_paragraph("昨天线上销售总额为{:,}元，线下销售总额为{:,}元，"
                       "总计销售额为{:,}元。".format(online_sale, shop_sale, online_sale + shop_sale))
# 缩进
pg.paragraph_format.left_indent = Mm(5)

doc.add_heading("线上数据分析", 1)
pg = doc.add_paragraph("各渠道类型销售金额分布如下：")
# 段落添加文字
pg.add_run("免费渠道为292189元，付费渠道为283272元。")
# 添加图片
doc.add_picture('./data_files/online_sale.png', width=Inches(5.5))

doc.add_paragraph("建议改进方案：")
doc.add_paragraph("增加付费广告投放", style="List Bullet")
doc.add_paragraph("提高着陆页加载速度", style="List Bullet")
doc.add_paragraph("减少页面跳转", style="List Bullet")

doc.add_heading("线下数据分析", 1)
# 添加表格
table = doc.add_table(rows=1, cols=3)
# 写表头
cells = table.rows[0].cells
cells[0].text = '城市'
cells[1].text = '销量'
# 写表体
records = [
    ['北京', '100'],
    ['上海', '120'],
    ['天津', '300'],
    ['河北', '200'],
    ['广东', '400'],
    ['辽宁', '500'],
    ['江苏', '700'],
    ['湖南', '600']
]
for city, amount in records:
    cells = table.add_row().cells
    cells[0].text = city
    cells[1].text = amount

doc.add_picture('./data_files/city_sale.png', width=Inches(5.5))

graph = doc.add_paragraph()
graph.paragraph_format.alignment = WD_PARAGRAPH_ALIGNMENT.RIGHT
# 字体加粗
graph.add_run('报告撰写人：').bold = True
graph.add_run('刘德华')

graph = doc.add_paragraph()
graph.paragraph_format.alignment = WD_PARAGRAPH_ALIGNMENT.RIGHT
graph.add_run('联系邮箱：').bold = True
run = graph.add_run('xxx@email.com')
# 字体
run.font.name = 'Times New Roman'
# 字体大小
run.font.size = Pt(12)
# 斜体
run.italic = True
# 下划线
run.underline = True

# 另起一页
doc.add_page_break()

graph = doc.add_paragraph()
graph.paragraph_format.alignment = WD_PARAGRAPH_ALIGNMENT.CENTER
run = graph.add_run('谢谢观看')
# 字体颜色
run.font.color.rgb = RGBColor(255, 0, 0)
# 引用样式
doc.add_paragraph('此报告为机密文件，请勿泄漏', style='Intense Quote')

doc.save('data_files/daily_report.docx')
```

### word转PDF

安装docx2pdf包

```python
from docx2pdf import convert

convert('./data_files/daily_report.docx', './data_files/daily_report.pdf')
```

## 处理PDF

### PDF读取

安装pdfminer包

```python
from pdfminer.converter import TextConverter
from pdfminer.layout import LAParams
from pdfminer.pdfdocument import PDFDocument
from pdfminer.pdfinterp import PDFResourceManager, PDFPageInterpreter
from pdfminer.pdfpage import PDFPage
from pdfminer.pdfparser import PDFParser
from io import StringIO

output_string = StringIO()
with open('./data_files/daily_report.pdf', 'rb') as f:
    # 从文件句柄创建一个pdf解析对象
    parser = PDFParser(f)
    # 创建pdf文档对象，存储文档结构
    doc = PDFDocument(parser)
    # 创建一个pdf资源管理对象，存储共享资源
    rsrcmgr = PDFResourceManager()
    # 创建一个device对象，指定参数，行距、边距等，这里使用默认参数
    device = TextConverter(rsrcmgr, output_string, laparams=LAParams())
    # 创建一个解释对象
    interpreter = PDFPageInterpreter(rsrcmgr, device)
    # 按⻚解析pdf文件
    for page in PDFPage.create_pages(doc):
        # 将内容读取到缓存
        interpreter.process_page(page)
# 打印出缓冲区的内容
print(output_string.getvalue())
```

### PDF添加水印

安装pypdf2包

新建一个空的word文档，不输入任何内容，只添加水印，然后导出成pdf。水印的透明度在word添加时设置。

```python
from PyPDF2 import PdfFileReader, PdfFileWriter

# 导入包含一个水印的pdf文件，只要有一⻚即可
watermark_pdf = PdfFileReader('./data_files/水印.pdf')
# 获取第一⻚
watermark = watermark_pdf.getPage(0)

# 把想要加水印的pdf文件加载进来
input_pdf = PdfFileReader('./data_files/daily_report.pdf')
# 创建一个writer对象，一会儿用来写新生成的pdf
writer = PdfFileWriter()

for page in range(input_pdf.getNumPages()):
    # 逐⻚读取pdf内的内容
    page = input_pdf.getPage(page)
    # 将当前⻚与水印⻚合并 
    page.mergePage(watermark)
    # 将当前⻚加入到待写入区域 
    writer.addPage(page)
    
# 将全部合并完的pdf保存到文件
with open('./data_files/包含水印.pdf', 'wb') as f:
    writer.write(f)
```

## 发送邮件

smtplib包，email包，py自带。

邮箱需要开启pop3/smtp功能。

### 普通邮件

```python
import smtplib
from email.mime.text import MIMEText

# 1. 设置服务器所需信息
# SMTP服务器域名
mail_host = 'smtp.163.com'
# 邮箱用户名
mail_user = 'xxx@163.com'
# 密码或授权码
mail_pass = 'AXNHIKAAAAAKJVRNR'
# 邮件发送方邮箱地址，有可能和用户名不一样
sender = 'goodpython@163.com'
# 邮件接收方邮箱地址，可以有多个收件人，所以用列表
receivers = ['xxx@qq.com']

# 2. 设置邮件内容
# 邮件正文
message = MIMEText('详情请⻅附件', 'plain', 'utf-8')
# 邮件主题
message['Subject'] = '每日运营报告'
# 发送方信息
message['From'] = sender
# 接受方信息
message['To'] = receivers[0]

# 3. 发送邮件 
try:
    smtpObj = smtplib.SMTP_SSL(mail_host, 465)
    # 设置日志级别，这样万一出错就会有详细的输出
    smtpObj.set_debuglevel(1)
    # 登录到服务器
    smtpObj.login(mail_user, mail_pass)
    # 发送
    smtpObj.sendmail(sender, receivers, message.as_string())
    smtpObj.quit()
    print('邮件发送成功')
except smtplib.SMTPException as e:
    print('error', e)
```

### 附件邮件

```python
import smtplib
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart 
from email.mime.image import MIMEImage

#设置登录及服务器信息
mail_host = 'smtp.163.com' 
mail_user = '159*****02' 
mail_pass = '7******x'
sender = '159*****02@163.com' 
receivers = ['7******0@qq.com']

#设置eamil信息
#添加一个MIMEmultipart类，处理正文及附件
message = MIMEMultipart()
message['From'] = sender
message['To'] = receivers[0]
message['Subject'] = 'title' 

#推荐使用HTML格式的正文内容，这样比较灵活，可以附加图片地址，调整格式等 
with open('abc.HTML','r') as f:
  content = f.read() 
  #设置HTML格式参数
  part1 = MIMEText(content,'HTML','utf-8') 
  
#添加一个txt文本附件
with open('abc.txt','r')as h:
  content2 = h.read()
  #设置txt参数
  part2 = MIMEText(content2,'plain','utf-8') 
  #附件设置内容类型，方便起⻅，设置为二进制流 
  part2['Content-Type'] = 'application/octet-stream' 
  #设置附件头，添加文件名
  part2['Content-Disposition'] = 'attachment;filename="abc.txt"' 

#添加照片附件
with open('1.png','rb')as fp:
  picture = MIMEImage(fp.read())
  #与txt文件设置相似
  picture['Content-Type'] = 'application/octet-stream'
  picture['Content-Disposition'] = 'attachment;filename="1.png"' 
  
#将内容附加到邮件主体中
message.attach(part1)
message.attach(part2)
message.attach(picture)
#登录并发送 
try:
  smtpObj = smtplib.SMTP() 
  smtpObj.connect(mail_host,25) 
  smtpObj.login(mail_user,mail_pass) 
  smtpObj.sendmail(sender,receivers,message.as_string()) 
  print('success')
  smtpObj.quit()
except smtplib.SMTPException as e:
	print('error',e)
```

# 爬虫

## requests库

requests库，python中最受欢迎的网络请求库。

```python
import requests

url = 'https://news.sina.com.cn/'
r = requests.get(url)
# 状态码不为200报错，停止运行
r.raise_for_status()

r.encoding = 'utf-8'
print(r.status_code)
print(r.text)
print(r.encoding)
print(r.url)
```

## http协议

url：

```http
协议://域名/统一资源标识符(uri,网站内路径)?请求参数key=请求参数value&请求参数key=请求参数value...#锚点

https://docs.python.org/zh-cn/3/library/csv.html?highlight=csv#module-csv
```

## beautifulsoup解析页面

安装beautifulsoup包

```python
from bs4 import BeautifulSoup

with open('./python.HTML') as f: 
  text = f.read()
  
# 以字符串对象创建BeautifulSoup对象
bs = BeautifulSoup(text, 'HTML.parser') 
# 工整的打印出所有HTML代码
print(bs.prettify()) 
# title节点的名称 
print(bs.title.name) 
# title节点的文本 
print(bs.title.text)
# title节点的父节点名称 
print(bs.title.parent.name) 
# 第一个p节点的父节点的所有子节点 
print(bs.p.parent.children) 
# p节点的class属性 
print(bs.p['class'])
# 第一个a标签节点
print(bs.a)
# 所有的a标签节点
print(bs.find_all('a'))
# 属性id为title1的第一个节点
print(bs.find(id='title1'))
# 找出所有class属性为paragraph的p标签，注意class是Python中的关键字，所以在传参数时加了一 个下划线。
bs.find_all('p', class_='paragraph')
# 第一个a节点
print(bs.find('a'))
# 找出所有的a节点，并打印他们的文本
for a in bs.find_all('a'):
	print(a.text)
# 获取文档内所有文字内容 
print(bs.get_text()) 
```

