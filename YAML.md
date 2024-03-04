# YAML( .yml)

YAML 是 "YAML Ain't a Markup Language"（YAML 不是一种标记语言）的递归缩写。在开发的这种语言时，YAML 的意思其实是："Yet Another Markup Language"（仍是一种标记语言）。

YAML 的语法和其他高级语言类似，并且可以格式化输出

- 输出 json 格式

  - -o json

- 仅打印资源名称

  - -o name

- 以纯文本格式输出所有信息

  - -o wide

- 输出 yaml 格式

  - -o yaml简单表达清单、散列表，标量等数据形态。它使用空白符号缩进和大量依赖外观的特色，特别适合用来表达或编辑数据结构、各种配置文件、倾印调试内容、文件大纲（例如：许多电子邮件标题格式和YAML非常接近）

### 基本语法

- 大小写敏感
- 使用缩进表示层级关系
- 缩进不允许使用tab，只允许空格
- 缩进的空格数不重要，只要相同层级的元素左对齐即可
- '#'表示注释

>对象：键值对的集合，又称为映射（mapping）/ 哈希（hashes） / 字典（dictionary）
>数组：一组按次序排列的值，又称为序列（sequence） / 列表（list）
>纯量（scalars）：单个的、不可再分的最基本的值

#### **1、纯量**

Yaml中的纯量包括：

- 字符串
- 布尔值
- 整数
- 浮点数
- Null
- 时间
- 日期

使用一个yaml例子来快速了解纯量的基本使用：

```yaml
boolean:
　　　　- TRUE #true、True都可以
　　　　- FALSE #false、False都可以
　　float:
　　　　- 3.14
　　　　- 6.8523015e+5 #可以使用科学计数法
　　int:
　　　　- 123
　　　　- 0b1010_0111_0100_1010_1110 #二进制表示
　　null:
　　　　parent: ~ #使用~表示null
　　string:
　　　　- 哈哈
　　　　- 'Hello world' #可以使用双引号或者单引号包裹特殊字符
　　　　- newline
　　　　  newline2 #字符串可以拆成多行，每一行会被转化成一个空格
　　date:
　　　　- 2018-02-17 #日期必须使用ISO 8601格式，即yyyy-MM-dd
　　datetime:
　　　　- 2018-02-17T15:02:31+08:00 #时间使用ISO 8601格式，时间和日期之间使用T连接，最后使用+代表时区
```

#### **2、对象**

对象为键值对，包含key和 value，中间用冒号分割，冒号后面要加一个空格。
key：描述对象属性的字符串。
value：可以是纯量、对象结合、数组。

样例：
    　　age: 18

​    　　key:
​        　　　　key1: value
​        　　　　key2: value2
​    　　如上是复合类型，value是包含2个对象的对象集合。对应Json的格式：key:{key1: value1, key2: value2}。

​    　　key:

​						\- A

​						\- B

​						\- C
​	value是包含3个元素的数组。

#### **3、数组**

以 - 开头的行表示构成一个数组：

\- A
\- B
\- C

YAML 支持多维数组。如下标识数组的子成员是一个数组：
  \- 

​		\- A

​		\- B

​		\- C

一个相对复杂的例子：
　　companies:
    　　　　-
        　　　　　　id: 1
        　　　　　　name: company1
        　　　　　　price: 200W
    　　　　-
        　　　　　　id: 2
        　　　　　　name: company2
        　　　　　　price: 500W
　　如上companies对应的value是一个数组，数组包含2个元素，每个数组元素又是包含3个对象的对象集合。

 

#### **4、更复杂的Key**

可以使用问号加一个空格代表一个复杂的 key，配合一个冒号加一个空格代表一个 value：
　　?
    　　　　- complexkey1
    　　　　- complexkey2
　　:
    　　　　- complexvalue1
    　　　　- complexvalue2
　　意思即对象的属性是一个数组 [complexkey1,complexkey2]，对应的值也是一个数组 [complexvalue1,complexvalue2]




#### **5、引用**

& 锚点和 * 别名，可以用来引用:
    　　defaults: &defaults
        　　　　adapter: postgres
        　　　　host: localhost

​    　　development:
​        　　　　database: myapp_development
​        　　　　<<: *defaults

​    　　test:
​        　　　　database: myapp_test
​        　　　　<<: *defaults
　　相当于:
​    　　defaults:
​        　　　　adapter: postgres
​        　　　　host: localhost

​    　　development:
​        　　　　database: myapp_development
​        　　　　adapter: postgres
​        　　　　host: localhost

​    　　test:
​        　　　　database: myapp_test
​        　　　　adapter: postgres
​        　　　　host: localhost
​    　　& 用来建立锚点（defaults），<< 表示合并到当前数据，* 用来引用锚点。

另一个例子:

\- &showell Steve
\- Clark
\- Brian
\- Oren
\- *showell

JavaScript 代码如下: [ 'Steve', 'Clark', 'Brian', 'Oren', 'Steve' ]