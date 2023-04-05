<a name="TNScx"></a>
# 返回页面文本编码部分
r.encoding = r.appear_encoding

- 将appear_encoding赋值给encoding
- appear_encoding是程序通过获取的页面文本内容解析该页面可能使用的编码方式，然后赋值给r（response）响应数据的编码方式
