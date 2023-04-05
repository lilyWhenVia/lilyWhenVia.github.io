---
    layout:     post
    title:      BeautifulSoup库
    date:       2023-04-05
    author:     lily
    header-img: img/爬虫学习.jpg
    catalog: 	 true
    tags:       爬虫学习
---

---
    layout:     post
    title:      BeautifulSoup库.md
    date:       2023-04-05
    author:     lily
    header-img: img/爬虫学习'.jpg
    catalog: 	 true
    tags:       爬虫学习
---

<a name="fxcrK"></a>
# bs4库提供的标签树的遍历
**_思维导图_**<br />![](https://cdn.nlark.com/yuque/0/2022/jpeg/1238904/1657893488759-a675f22b-080b-4b66-bf2b-6ba9d2cf0608.jpeg)

---

标签
<a name="f6imP"></a>
# 提供用于数据提取的相关函数以及方法和属性
<a name="n9AXe"></a>
## .find_all
<a name="kuBGe"></a>
### 具体实现功能

1. 返回该标签下（标签内）的所有信息，只返回一个结果，相当于检索哪个标签一股脑把该标签索取出来。
2. 返回类型是<class 'bs4.element.ResultSet'>     结果集   ，返回一个列表结果
3. 遍历时只有一个对象
4. ![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1658634691147-5aa2b393-22be-43a0-8126-e70702b60e3c.png#clientId=u0440bcbd-31b2-4&from=paste&height=273&id=bTqXv&name=image.png&originHeight=409&originWidth=1251&originalType=binary&ratio=1&rotation=0&showTitle=false&size=94221&status=done&style=none&taskId=u82e9a961-e267-49c2-b2da-6dbed4e5ef6&title=&width=834)

<a name="JVM99"></a>
## 获取标签

```python
#换行输出soup里的所有标签名称
for tag in soup.fina_all(True):
    print(tag)
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1658646580140-40641592-4c6e-48d8-bfef-50188650e68e.png#clientId=u11f063ad-c422-4&from=paste&height=239&id=u5382d5d0&name=image.png&originHeight=358&originWidth=505&originalType=binary&ratio=1&rotation=0&showTitle=false&size=60182&status=done&style=stroke&taskId=uafe6579a-3c43-432a-983b-e493b17a587&title=&width=336.6666666666667)

<a name="lcg4K"></a>
## 用RE检索tag.name带有特殊字符的标签
连接直达：<br />[https://www.icourse163.org/learn/BIT-1001870001?tid=1467473478#/learn/content?type=detail&id=1248909690&cid=1276198025](https://www.icourse163.org/learn/BIT-1001870001?tid=1467473478#/learn/content?type=detail&id=1248909690&cid=1276198025)<br />课程名称：基于bs4库的HTML内容查找方法<br />时间：2：35处<br />**实现方法：**
```python
import re
#r.compile("b")在正则中可用于检索以b开头的所有%整个%标签(不是标签name)
for tag in soup.find_all(re.compile('b')):
    print(tag.name)
```
<a name="MfPCi"></a>
## 检索tag.attrs带有特殊属性的标签
```python
#检索带有course属性的p标签
soup.find_all('p', "course")
#=========输出结果======
#返回所有符合条件p标签组成的列表
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1658647435107-44ba53d3-4689-4232-8968-bc42509cb3aa.png#clientId=u11f063ad-c422-4&from=paste&height=149&id=u4eae715d&name=image.png&originHeight=223&originWidth=897&originalType=binary&ratio=1&rotation=0&showTitle=false&size=266320&status=done&style=none&taskId=u1107bda3-8216-4dd7-b4c2-2341be8b623&title=&width=598)

<a name="ZuIXo"></a>
## 检索带有特定符号的标签
检索范围为tag.name/tag.attrs
```python
import re
for tag in soup.BeautifulSoup(re.compile("link")):
    print(tag)
#返回结果为列表
#遍历该列表得到各个符合的标签
```
<a name="E5UbM"></a>
## 检索含特定字符串的标签
检索范围tag.string
```python
import re
soup.find_all(string = re.compile("xxx"))
#获取string部分带有xxx的tag
#返回符合条件的tag组成的列表
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1658648848120-66fbb1ca-538f-4ce2-a4f9-96df80abf3b2.png#clientId=u11f063ad-c422-4&from=paste&height=119&id=u80c4bf27&name=image.png&originHeight=178&originWidth=907&originalType=binary&ratio=1&rotation=0&showTitle=false&size=144033&status=done&style=none&taskId=u06937c3e-dee9-4474-8171-1e4839f54a0&title=&width=604.6666666666666)

---

<a name="fWkmS"></a>
## .find——find_all（）函数的扩展

1. .find函数同.find_all函数，但只返回第一个查找到的标签
2. 返回类型是<class 'bs4.element.Tag'>    tag类型
3. 将find函数返回对象遍历时，会把子标签逐一返回，结果类似遍历迭代器或是列表，

  抑或类似于遍历了tag，返回其子节点

4. ![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1658062248920-7b5554c4-7a08-4f02-bd00-de5285368cec.png#clientId=u310c24df-3409-4&from=paste&height=717&id=ue32bb29d&name=image.png&originHeight=1075&originWidth=1915&originalType=binary&ratio=1&rotation=0&showTitle=false&size=195217&status=done&style=none&taskId=u9f9a5437-dec4-42a8-b17f-9432ed0b18e&title=&width=1276.6666666666667)
<a name="knPYf"></a>
## 其余扩展方法
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1658648985394-b7e43372-abad-4561-80bf-734dfcb18d9d.png#clientId=u11f063ad-c422-4&from=paste&height=363&id=u75c75860&name=image.png&originHeight=545&originWidth=1156&originalType=binary&ratio=1&rotation=0&showTitle=false&size=533337&status=done&style=none&taskId=ud3be9094-f205-48a5-989a-8a03ea4ae4b&title=&width=770.6666666666666)
<a name="acDA2"></a>
#### <br />
<a name="jLDl2"></a>
# 三种信息的标记形式:
HTML:www（world wide web）公认的信息组织方式，它通过预定义的标签形式组织不同类型的信息

---

<a name="wWu0Q"></a>
## 标记信息的三种基本格式（常见的）
<a name="mAu6I"></a>
### XML:

   1. 以标签为主来构建信息的表达方式
   2. ![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1657946231613-f54b41e1-f083-4221-93dd-9862f626281f.png#clientId=u022b2c3e-4b11-4&from=paste&height=226&id=g2dLF&name=image.png&originHeight=339&originWidth=526&originalType=binary&ratio=1&rotation=0&showTitle=false&size=72853&status=done&style=none&taskId=ufdb124a3-29a9-4a3c-9a15-2a6fa574c33&title=&width=350.6666666666667)
      1. 当标签中有内容时，以一对尖括号囊括
      2. 没有内容时一个尖括号
      3. 注释也使用一对尖括号囊括
<a name="T8ki1"></a>
### JOSN：

   3. JavaScript里的一种格式，有类型的键值对（键和值都要是一种明确的数据类型）
      1. 可以对信息属性定义的为键，对信息值得描述为值
      2. ![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1657946753355-986f594a-b318-4529-b4c5-b3d32d88d1d9.png#clientId=u022b2c3e-4b11-4&from=paste&height=216&id=VQKJ3&name=image.png&originHeight=324&originWidth=1044&originalType=binary&ratio=1&rotation=0&showTitle=false&size=183027&status=done&style=none&taskId=uf5a76cb9-be11-498d-8190-07956103996&title=&width=696)
   4. ![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1657947082392-06b365ff-7cff-4772-8944-6bb87294b1c1.png#clientId=u022b2c3e-4b11-4&from=paste&height=207&id=u4dedea5b&name=image.png&originHeight=310&originWidth=955&originalType=binary&ratio=1&rotation=0&showTitle=false&size=81921&status=done&style=none&taskId=u5e148a29-5c6a-44b9-8b61-1352a92ed04&title=&width=636.6666666666666)

三种表示类型

      1. 单一键值对
      2. 一个键对应多个值，用[  ]括起来，用逗号隔开
      3. 嵌套类型键值对，一个键得值可以包括多个键值对，用{  }括起来，换行隔开
<a name="Dop4V"></a>
### YAML

   1. 无类型的键值对，key与value没有类型
   2. ![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1657975073045-a702abcc-1baf-42e2-8d61-8ca0b7270dd8.png#clientId=u022b2c3e-4b11-4&from=paste&height=387&id=u3fd3cb5c&name=image.png&originHeight=581&originWidth=893&originalType=binary&ratio=1&rotation=0&showTitle=false&size=72594&status=done&style=none&taskId=u1c031e5f-a11c-40a2-93b9-fcfde8e0723&title=&width=595.3333333333334)
      1. 单个键值
      2. 一个键多个值，用”-“表示并列关系，‘#’可用于注释，可用 ”|“来表示整块数据（例如一大段包括换行的文字）
      3. 嵌套键值对，用缩进表示所属关系

<a name="mldGh"></a>
# bs4库中主要元素及相关操作
<a name="ydmWr"></a>
## 相关元素
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1658643547793-fb8d5fbd-869f-4593-9f5a-29f5dc54e5a3.png#clientId=ub2aad822-54bb-4&from=paste&height=369&id=uc26b1686&name=image.png&originHeight=553&originWidth=1165&originalType=binary&ratio=1&rotation=0&showTitle=false&size=429921&status=done&style=none&taskId=ud0db864f-6cde-40c2-a70c-8b5e22f52a0&title=&width=776.6666666666666)

1. name
2. attrs
3. recursive
4. NagnivableString  ——   string

<a name="pg1VW"></a>
##   bs4库可使用的解析器![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1658643392931-b7c6f987-0006-4f10-9c80-e31ca37d6742.png#clientId=ub2aad822-54bb-4&from=paste&height=381&id=u5f9b4ffe&name=image.png&originHeight=571&originWidth=1122&originalType=binary&ratio=1&rotation=0&showTitle=false&size=391507&status=done&style=none&taskId=u42899de0-5ab9-49cb-a89e-dfd3c08e800&title=&width=748)

---

- 从标签中获取属性用  tag.get('属性')  使用get方法
- mooc相关内容课程直达：
   - [https://www.icourse163.org/learn/BIT-1001870001?tid=1467473478#/learn/content?type=detail&id=1248909689&cid=1276198017&replay=true](https://www.icourse163.org/learn/BIT-1001870001?tid=1467473478#/learn/content?type=detail&id=1248909689&cid=1276198017&replay=true)
<a name="Zgqjw"></a>
## 提取信息实例

1. 提取网页中所有url
   1. 搜索到url所在的<a>（a标签）
   2. 解析<a>，在href属性中获得url

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1658643624469-348c1c6d-142e-4d5a-b1e4-dd5f54482b16.png#clientId=ub2aad822-54bb-4&from=paste&height=252&id=u32d0ebbb&name=image.png&originHeight=378&originWidth=1175&originalType=binary&ratio=1&rotation=0&showTitle=false&size=329402&status=done&style=none&taskId=u9665cc2b-5a8d-4d99-ba09-0cf24371cbd&title=&width=783.3333333333334)<br />[http://python123.io/ws/demo.html](http://python123.io/ws/demo.html)
<a name="NuzZb"></a>
### 获取单个上级标签（父亲标签）
**获取父亲标签的名字，为字符串形式**<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1658643968143-6728974b-ff12-4056-8a19-ceb4c5092559.png#clientId=ub2aad822-54bb-4&from=paste&height=201&id=uf9505621&name=image.png&originHeight=302&originWidth=641&originalType=binary&ratio=1&rotation=0&showTitle=false&size=132685&status=done&style=none&taskId=ucdbbf266-c3c7-4d6c-a44e-b1f259a6f49&title=&width=427.3333333333333)
<a name="bKWwi"></a>
### 获取标签属性attrs
**标签属性为字典类型，可以通过键来索取值**<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1658644208107-df728240-b2dc-4d72-9e6e-e0ec989e29d3.png#clientId=ub2aad822-54bb-4&from=paste&height=253&id=u4796e9d6&name=image.png&originHeight=379&originWidth=903&originalType=binary&ratio=1&rotation=0&showTitle=false&size=207664&status=done&style=none&taskId=u8edaf471-8f2e-473f-85ad-506aa7d6a34&title=&width=602)

<a name="p2Ke9"></a>
### NavigableString的获取
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1658644707408-1f148a8e-bb1f-40cc-b5bd-6350fed89c95.png#clientId=ub2aad822-54bb-4&from=paste&height=266&id=ubd5d0549&name=image.png&originHeight=399&originWidth=970&originalType=binary&ratio=1&rotation=0&showTitle=false&size=293231&status=done&style=none&taskId=ud82e8e9a-7f79-407f-86ea-0ad0bba898a&title=&width=646.6666666666666)<br />**但必须要注意的是.string方法也可以获取到注释内容，并且把注释部分的<!-->自动过滤，造成打印出来的信息内容和NagviableString类型外观一致，但实际上它是注释类型。**<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1658644878971-cc3b11b2-6706-42a0-a3b2-eef8b499d1cc.png#clientId=ub2aad822-54bb-4&from=paste&height=205&id=u7bfc6f44&name=image.png&originHeight=307&originWidth=900&originalType=binary&ratio=1&rotation=0&showTitle=false&size=220042&status=done&style=none&taskId=uc4cf7268-513e-4fcc-ad7e-f18fed6e7db&title=&width=600)<br />**注意：**<br />** string：获取某个标签下的非标签**[**字符串**](https://so.csdn.net/so/search?q=%E5%AD%97%E7%AC%A6%E4%B8%B2&spm=1001.2101.3001.7020)**(如果自身标签下没有内容，则返回空值，即使子孙有)，返回的是一个字符串。**
<a name="uC1wC"></a>
#### 小结
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1658645294356-675f311c-f76c-481c-8307-b64fd1ae4555.png#clientId=u11f063ad-c422-4&from=paste&height=423&id=ud1ad028d&name=image.png&originHeight=635&originWidth=1195&originalType=binary&ratio=1&rotation=0&showTitle=false&size=735129&status=done&style=none&taskId=uc3065a41-c6a2-4d0d-bf1a-75a62f116b2&title=&width=796.6666666666666)
---
    layout:     post
    title:      2023-04-05-BeautifulSoup库
    date:       2023-04-05
    author:     lily
    header-img: img/爬虫学习.jpg
    catalog: 	 true
    tags:       爬虫学习
---

---
    layout:     post
    title:      BeautifulSoup库.md
    date:       2023-04-05
    author:     lily
    header-img: img/爬虫学习'.jpg
    catalog: 	 true
    tags:       爬虫学习
---

<a name="fxcrK"></a>
# bs4库提供的标签树的遍历
**_思维导图_**<br />![](https://cdn.nlark.com/yuque/0/2022/jpeg/1238904/1657893488759-a675f22b-080b-4b66-bf2b-6ba9d2cf0608.jpeg)

---

标签
<a name="f6imP"></a>
# 提供用于数据提取的相关函数以及方法和属性
<a name="n9AXe"></a>
## .find_all
<a name="kuBGe"></a>
### 具体实现功能

1. 返回该标签下（标签内）的所有信息，只返回一个结果，相当于检索哪个标签一股脑把该标签索取出来。
2. 返回类型是<class 'bs4.element.ResultSet'>     结果集   ，返回一个列表结果
3. 遍历时只有一个对象
4. ![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1658634691147-5aa2b393-22be-43a0-8126-e70702b60e3c.png#clientId=u0440bcbd-31b2-4&from=paste&height=273&id=bTqXv&name=image.png&originHeight=409&originWidth=1251&originalType=binary&ratio=1&rotation=0&showTitle=false&size=94221&status=done&style=none&taskId=u82e9a961-e267-49c2-b2da-6dbed4e5ef6&title=&width=834)

<a name="JVM99"></a>
## 获取标签

```python
#换行输出soup里的所有标签名称
for tag in soup.fina_all(True):
    print(tag)
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1658646580140-40641592-4c6e-48d8-bfef-50188650e68e.png#clientId=u11f063ad-c422-4&from=paste&height=239&id=u5382d5d0&name=image.png&originHeight=358&originWidth=505&originalType=binary&ratio=1&rotation=0&showTitle=false&size=60182&status=done&style=stroke&taskId=uafe6579a-3c43-432a-983b-e493b17a587&title=&width=336.6666666666667)

<a name="lcg4K"></a>
## 用RE检索tag.name带有特殊字符的标签
连接直达：<br />[https://www.icourse163.org/learn/BIT-1001870001?tid=1467473478#/learn/content?type=detail&id=1248909690&cid=1276198025](https://www.icourse163.org/learn/BIT-1001870001?tid=1467473478#/learn/content?type=detail&id=1248909690&cid=1276198025)<br />课程名称：基于bs4库的HTML内容查找方法<br />时间：2：35处<br />**实现方法：**
```python
import re
#r.compile("b")在正则中可用于检索以b开头的所有%整个%标签(不是标签name)
for tag in soup.find_all(re.compile('b')):
    print(tag.name)
```
<a name="MfPCi"></a>
## 检索tag.attrs带有特殊属性的标签
```python
#检索带有course属性的p标签
soup.find_all('p', "course")
#=========输出结果======
#返回所有符合条件p标签组成的列表
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1658647435107-44ba53d3-4689-4232-8968-bc42509cb3aa.png#clientId=u11f063ad-c422-4&from=paste&height=149&id=u4eae715d&name=image.png&originHeight=223&originWidth=897&originalType=binary&ratio=1&rotation=0&showTitle=false&size=266320&status=done&style=none&taskId=u1107bda3-8216-4dd7-b4c2-2341be8b623&title=&width=598)

<a name="ZuIXo"></a>
## 检索带有特定符号的标签
检索范围为tag.name/tag.attrs
```python
import re
for tag in soup.BeautifulSoup(re.compile("link")):
    print(tag)
#返回结果为列表
#遍历该列表得到各个符合的标签
```
<a name="E5UbM"></a>
## 检索含特定字符串的标签
检索范围tag.string
```python
import re
soup.find_all(string = re.compile("xxx"))
#获取string部分带有xxx的tag
#返回符合条件的tag组成的列表
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1658648848120-66fbb1ca-538f-4ce2-a4f9-96df80abf3b2.png#clientId=u11f063ad-c422-4&from=paste&height=119&id=u80c4bf27&name=image.png&originHeight=178&originWidth=907&originalType=binary&ratio=1&rotation=0&showTitle=false&size=144033&status=done&style=none&taskId=u06937c3e-dee9-4474-8171-1e4839f54a0&title=&width=604.6666666666666)

---

<a name="fWkmS"></a>
## .find——find_all（）函数的扩展

1. .find函数同.find_all函数，但只返回第一个查找到的标签
2. 返回类型是<class 'bs4.element.Tag'>    tag类型
3. 将find函数返回对象遍历时，会把子标签逐一返回，结果类似遍历迭代器或是列表，

  抑或类似于遍历了tag，返回其子节点

4. ![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1658062248920-7b5554c4-7a08-4f02-bd00-de5285368cec.png#clientId=u310c24df-3409-4&from=paste&height=717&id=ue32bb29d&name=image.png&originHeight=1075&originWidth=1915&originalType=binary&ratio=1&rotation=0&showTitle=false&size=195217&status=done&style=none&taskId=u9f9a5437-dec4-42a8-b17f-9432ed0b18e&title=&width=1276.6666666666667)
<a name="knPYf"></a>
## 其余扩展方法
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1658648985394-b7e43372-abad-4561-80bf-734dfcb18d9d.png#clientId=u11f063ad-c422-4&from=paste&height=363&id=u75c75860&name=image.png&originHeight=545&originWidth=1156&originalType=binary&ratio=1&rotation=0&showTitle=false&size=533337&status=done&style=none&taskId=ud3be9094-f205-48a5-989a-8a03ea4ae4b&title=&width=770.6666666666666)
<a name="acDA2"></a>
#### <br />
<a name="jLDl2"></a>
# 三种信息的标记形式:
HTML:www（world wide web）公认的信息组织方式，它通过预定义的标签形式组织不同类型的信息

---

<a name="wWu0Q"></a>
## 标记信息的三种基本格式（常见的）
<a name="mAu6I"></a>
### XML:

   1. 以标签为主来构建信息的表达方式
   2. ![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1657946231613-f54b41e1-f083-4221-93dd-9862f626281f.png#clientId=u022b2c3e-4b11-4&from=paste&height=226&id=g2dLF&name=image.png&originHeight=339&originWidth=526&originalType=binary&ratio=1&rotation=0&showTitle=false&size=72853&status=done&style=none&taskId=ufdb124a3-29a9-4a3c-9a15-2a6fa574c33&title=&width=350.6666666666667)
      1. 当标签中有内容时，以一对尖括号囊括
      2. 没有内容时一个尖括号
      3. 注释也使用一对尖括号囊括
<a name="T8ki1"></a>
### JOSN：

   3. JavaScript里的一种格式，有类型的键值对（键和值都要是一种明确的数据类型）
      1. 可以对信息属性定义的为键，对信息值得描述为值
      2. ![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1657946753355-986f594a-b318-4529-b4c5-b3d32d88d1d9.png#clientId=u022b2c3e-4b11-4&from=paste&height=216&id=VQKJ3&name=image.png&originHeight=324&originWidth=1044&originalType=binary&ratio=1&rotation=0&showTitle=false&size=183027&status=done&style=none&taskId=uf5a76cb9-be11-498d-8190-07956103996&title=&width=696)
   4. ![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1657947082392-06b365ff-7cff-4772-8944-6bb87294b1c1.png#clientId=u022b2c3e-4b11-4&from=paste&height=207&id=u4dedea5b&name=image.png&originHeight=310&originWidth=955&originalType=binary&ratio=1&rotation=0&showTitle=false&size=81921&status=done&style=none&taskId=u5e148a29-5c6a-44b9-8b61-1352a92ed04&title=&width=636.6666666666666)

三种表示类型

      1. 单一键值对
      2. 一个键对应多个值，用[  ]括起来，用逗号隔开
      3. 嵌套类型键值对，一个键得值可以包括多个键值对，用{  }括起来，换行隔开
<a name="Dop4V"></a>
### YAML

   1. 无类型的键值对，key与value没有类型
   2. ![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1657975073045-a702abcc-1baf-42e2-8d61-8ca0b7270dd8.png#clientId=u022b2c3e-4b11-4&from=paste&height=387&id=u3fd3cb5c&name=image.png&originHeight=581&originWidth=893&originalType=binary&ratio=1&rotation=0&showTitle=false&size=72594&status=done&style=none&taskId=u1c031e5f-a11c-40a2-93b9-fcfde8e0723&title=&width=595.3333333333334)
      1. 单个键值
      2. 一个键多个值，用”-“表示并列关系，‘#’可用于注释，可用 ”|“来表示整块数据（例如一大段包括换行的文字）
      3. 嵌套键值对，用缩进表示所属关系

<a name="mldGh"></a>
# bs4库中主要元素及相关操作
<a name="ydmWr"></a>
## 相关元素
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1658643547793-fb8d5fbd-869f-4593-9f5a-29f5dc54e5a3.png#clientId=ub2aad822-54bb-4&from=paste&height=369&id=uc26b1686&name=image.png&originHeight=553&originWidth=1165&originalType=binary&ratio=1&rotation=0&showTitle=false&size=429921&status=done&style=none&taskId=ud0db864f-6cde-40c2-a70c-8b5e22f52a0&title=&width=776.6666666666666)

1. name
2. attrs
3. recursive
4. NagnivableString  ——   string

<a name="pg1VW"></a>
##   bs4库可使用的解析器![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1658643392931-b7c6f987-0006-4f10-9c80-e31ca37d6742.png#clientId=ub2aad822-54bb-4&from=paste&height=381&id=u5f9b4ffe&name=image.png&originHeight=571&originWidth=1122&originalType=binary&ratio=1&rotation=0&showTitle=false&size=391507&status=done&style=none&taskId=u42899de0-5ab9-49cb-a89e-dfd3c08e800&title=&width=748)

---

- 从标签中获取属性用  tag.get('属性')  使用get方法
- mooc相关内容课程直达：
   - [https://www.icourse163.org/learn/BIT-1001870001?tid=1467473478#/learn/content?type=detail&id=1248909689&cid=1276198017&replay=true](https://www.icourse163.org/learn/BIT-1001870001?tid=1467473478#/learn/content?type=detail&id=1248909689&cid=1276198017&replay=true)
<a name="Zgqjw"></a>
## 提取信息实例

1. 提取网页中所有url
   1. 搜索到url所在的<a>（a标签）
   2. 解析<a>，在href属性中获得url

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1658643624469-348c1c6d-142e-4d5a-b1e4-dd5f54482b16.png#clientId=ub2aad822-54bb-4&from=paste&height=252&id=u32d0ebbb&name=image.png&originHeight=378&originWidth=1175&originalType=binary&ratio=1&rotation=0&showTitle=false&size=329402&status=done&style=none&taskId=u9665cc2b-5a8d-4d99-ba09-0cf24371cbd&title=&width=783.3333333333334)<br />[http://python123.io/ws/demo.html](http://python123.io/ws/demo.html)
<a name="NuzZb"></a>
### 获取单个上级标签（父亲标签）
**获取父亲标签的名字，为字符串形式**<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1658643968143-6728974b-ff12-4056-8a19-ceb4c5092559.png#clientId=ub2aad822-54bb-4&from=paste&height=201&id=uf9505621&name=image.png&originHeight=302&originWidth=641&originalType=binary&ratio=1&rotation=0&showTitle=false&size=132685&status=done&style=none&taskId=ucdbbf266-c3c7-4d6c-a44e-b1f259a6f49&title=&width=427.3333333333333)
<a name="bKWwi"></a>
### 获取标签属性attrs
**标签属性为字典类型，可以通过键来索取值**<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1658644208107-df728240-b2dc-4d72-9e6e-e0ec989e29d3.png#clientId=ub2aad822-54bb-4&from=paste&height=253&id=u4796e9d6&name=image.png&originHeight=379&originWidth=903&originalType=binary&ratio=1&rotation=0&showTitle=false&size=207664&status=done&style=none&taskId=u8edaf471-8f2e-473f-85ad-506aa7d6a34&title=&width=602)

<a name="p2Ke9"></a>
### NavigableString的获取
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1658644707408-1f148a8e-bb1f-40cc-b5bd-6350fed89c95.png#clientId=ub2aad822-54bb-4&from=paste&height=266&id=ubd5d0549&name=image.png&originHeight=399&originWidth=970&originalType=binary&ratio=1&rotation=0&showTitle=false&size=293231&status=done&style=none&taskId=ud82e8e9a-7f79-407f-86ea-0ad0bba898a&title=&width=646.6666666666666)<br />**但必须要注意的是.string方法也可以获取到注释内容，并且把注释部分的<!-->自动过滤，造成打印出来的信息内容和NagviableString类型外观一致，但实际上它是注释类型。**<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1658644878971-cc3b11b2-6706-42a0-a3b2-eef8b499d1cc.png#clientId=ub2aad822-54bb-4&from=paste&height=205&id=u7bfc6f44&name=image.png&originHeight=307&originWidth=900&originalType=binary&ratio=1&rotation=0&showTitle=false&size=220042&status=done&style=none&taskId=uc4cf7268-513e-4fcc-ad7e-f18fed6e7db&title=&width=600)<br />**注意：**<br />** string：获取某个标签下的非标签**[**字符串**](https://so.csdn.net/so/search?q=%E5%AD%97%E7%AC%A6%E4%B8%B2&spm=1001.2101.3001.7020)**(如果自身标签下没有内容，则返回空值，即使子孙有)，返回的是一个字符串。**
<a name="uC1wC"></a>
#### 小结
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1658645294356-675f311c-f76c-481c-8307-b64fd1ae4555.png#clientId=u11f063ad-c422-4&from=paste&height=423&id=ud1ad028d&name=image.png&originHeight=635&originWidth=1195&originalType=binary&ratio=1&rotation=0&showTitle=false&size=735129&status=done&style=none&taskId=uc3065a41-c6a2-4d0d-bf1a-75a62f116b2&title=&width=796.6666666666666)
