## 正则表达式字符匹配攻略
正则表达式是匹配模式，要么匹配字符、要么匹配位置
### 两种模糊匹配
- 横向模糊匹配
  含义：一个正则可匹配的字符串的长度不是固定的、可以是多种情况的。其实现方式是使用量词，譬如：{m, n},表示连续出现最少m次，最多n次
  ````js
  var regex = /ab{2,5}c/g
  var str = 'abcabbbcabbc'
  str.match(regex)
  // ["abbc", "abbbc"]
  ````
- 纵向模糊匹配
  含义：一个正则匹配的字符串，具体到某一个字符时，它可以不是某个确定的字符，可以有多种可能。
  其实现方式是使用字符组，譬如[abc]，表示该字符可以是"a","b","c"中的任意一个
  ````js
  var reg = /a[123]b/g
  var str = 'a1b a2b a3b a4b'
  str.match(reg)
  // ["a1b", "a2b", "a3b"]
  ````
### 字符组
- 范围表示法
  如果字符组里面的字符特别多的话，怎么办？可以使用范围表示法
  例如：[123456abcdefg]，可以写成[1-6a-g]表示
  因为连字符-有特殊用途，如果要匹配"a","-", "z"中的任意一个字符，该怎么做？
  不能写成[a-z]，因为其表示小写字符中的任意一个
  可以写成如下形式[-az],[az-],[a\-z] 要么放在开头、要么放在结尾、要么转义。总之不会让引擎认为是范围表示反就行了。
- 排除字符组
  脱字符^，[\^abc]表示，不能是a,b,c中的任意一个
- 常见的简写形式
  \d 表示[0-9]。表示一位数字
  \D 表示\[^0-9]。表示除数字外的任意字符
  \w 表示[0-9a-zA-Z_]。表示数字，大小写字母、下划线
  \W 表示\[^0-9a-zA-Z_]。表示非单词字符
  \s 表示[ \t\v\n\r\f]。表示空白符，包括：空格，水平制表符，垂直制表符，换行符，回车符，换页符
  \S 表示\[^ \t\v\n\r\f]。表示非空白符
  .  表示\[^\n\r\u2028\u2029]。通配符，表示几乎任意字符。换行符、回车符、行分隔符和段分隔符除外
  如果要匹配任意字符怎么办？可以使用[\d\D],[\w\W],[\s\S],[^]中的任何一个
### 量词
  量词也成为了重复，掌握{2,5}的含义后只需要记住一些简写形式
  - 简写形式
    {m,} 表示至少出现m次
    {m}  等价于{m,m}，表示出现m次
    ?    等价于{0,1}，表示出现或者不出现
    +    等价于{1,} ，表示至少出现一次
    *    等价于{0,} ，表示出现任意次，有可能不出现
  - 贪婪匹配与惰性匹配
    通过在量词后面加个问号就能实现惰性匹配，因为所有惰性匹配的形式如下

  <table>
    <thead>
      <th>惰性量词</th>
      <th>贪婪量词</th>
    </thead>
    <tbody>
      <tr>
        <td>{m,n}?</td>
        <td>{m,n}</td>
      </tr>
      <tr>
        <td>{m,}?</td>
        <td>{m,}</td>
      </tr>
      <tr>
        <td>??</td>
        <td>?</td>
      </tr>
      <tr>
        <td>+?</td>
        <td>+</td>
      </tr>
      <tr>
        <td>*?</td>
        <td>*</td>
      </tr>
    <tbody>
  </table>

### 分支结构
一个模式可以实现横向纵向匹配。而多选分支可以支持多个子模式任选其一。
具体形式如下：(p1|p2|p3)其中p1,p2,p3是子模式，用|(管道符)分隔，表示其中任何之一

### 案例分析

匹配字符，无非就是字符组、量词和分支结构的组合
1. 匹配十六进制颜色值
   要求匹配 #ebebeb #FFe
   分析：表示一个16进制字符可以用[0-9a-fA-F]，其中字符可以出现3次或6次
   ````js
   var regex = /#([0-9a-fA-F]{6}|[0-9a-fA-F]{3})/g
   var str = "#ffbbad #Fc01DF #FFF #ffE"
   console.log(str.match(regex))
   // => ["#ffbbad", "#Fc01DF", "#FFF", "#ffE"]
   ````
2. 匹配时间