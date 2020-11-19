## 数据库

### MongoDB

mongodb数据库父子关系是：数据库 → 集合 → 数据

#### 初始化

- 新建一个存放数据库的文件夹（路径不能含中文）
- 启动MongoDb服务

```
服务端： cmd里输入 mongod --dbpath +存放文件夹路径
如：mongod --dbpath C:\Users\MI\Desktop\MongoDb 
MongoDb中，存在物理文件，存储在对应的文件夹，可以用U盘拷走

注意：此cmd窗口不能关闭，不能ctrl + c 否则数据库就会自动关闭

一个电脑里只能指定一个文件夹当服务器 即使另建一个文件夹并 dbpath 访问的还是共同的数据库
```

- 客户端

```
在保持服务端cmd开启的情况下另开一个cmd 输入 mongo 即可连接到本地的数据库
    远程连接则 mongo + ip地址+端口 
```

#### 数据库指令

##### 常规使用

- 杂项

```
清屏:
	cls

退出MongoDb：   
	exti 
```

- 查看所有数据库列表

```
show dbs
```

- 使用数据库、创建数据库

```
use +db_name 

如果原来不存在数据库，则执行创建该数据库步骤，但是想要成功创建该数据库，则必须要在该数据库中创建集合
```

- 创建集合（需要先进入数据库）

```mysql
db.createCollection(collention_name [,options])

# options: 可选参数, 指定有关内存大小及索引的选项
db.createCollection("students")		# 创建 students 集合，注意有引号
```

- 插入数据（需要先进入数据库）

```mysql
db.createCollection_name.insert()

db.students.insert({"name":"Lucy","age":20,"sex":"男"})

# 在该数据库下的stuents集合中插入{"name":"Lucy","age":20,"sex":"男"}数据
```

- 显示当前数据库的所有集合

```
show collections
```

- 删除当前所在数据库

```
db.dropDatabase()
```

- 删除指定的集合

```
db.createCollection_name.drop() 
db.students.drop()
```

-----

##### 查找数据

```
条件写法有两种：
find({属性：属性值})
find({属性值})
```

- 一般查询：

```sql
db.集合名称.find()  （在对应的数据库下使用 use xxx 多条数据会分行显示） 
    -- db.students 相当于 select *from students

查询并过滤掉重复数据
    db.集合名称.distinct("属性")
        -- db.students.distinct("name") 相当于 select distict name from students    会过滤掉有拥有name属性并且数值相等的对象

升序和降序:
	db.集合名称.find().sort({属性: 1})  
    	-- db.students.find().sort({age: 1})    按照age正序排列
    db.集合名称.find().sort({属性: -1})
        -- db.students.find().sort({age: -1})    按照age倒序排列

查询前n条数据:
    db.集合名称.find().limit(n)
        -- db.students.find().limit(5) 相当于 selecttop 5 * from students

查询n条以后的数据:
    db.集合名称.find().skip(n)
        -- db.students.find().skip(10) 相当于select * from students where id not in ( selecttop 10 * from students )

查询从第n开始之后的m条数据:
    db.集合名称.find().skip(n).limit(m)     -- 返回m条数据

查询在 n-m 之间的数据:
    db.userInfo.find().limit(m).skip(n)     -- 返回m-n条数据
        -- db.userInfo.find().limit(10).skip(5) 

查询第一条数据:
    db.集合名称.findOne()   -- 相当于 selecttop 1 * from 集合名称
```

- 统计个数

  方法count()用于统计结果集中文档条数

```mysql
# 以下两种语法功能一样：
db.集合名称.find({条件}).count()	
	# db.stu.find({gender:1}).count()	统计男生人数
db.集合名称.count({条件})
	# db.stu.count({age:{$gt:20},gender:1})	  统计年龄大于20的男生人数
	
# 如果没有条件则表示查找该集合的所有数据条数
db.集合名称.count()
```

- 条件查询：

```sql
等于:
db.集合名称.find({"属性": 数值}) 
    -- db.students.find({"age": 22}) 相当于 select * from students where age = 22
        -- db.students.find({"age": 22 , "name":"Lucy"}) 相当于 select * from students where age = 22 and name = Lucy
大于:
    db.集合名称.find({属性: {$gt: 数值}})
        -- db.students.find({age: {$gt: 22}}) 相当于 select * from students where age >22
小于:
    db.集合名称.find({属性: {$lt: 数值}})
        -- db.students.find({age: {$lt: 22}}) 相当于 select * from students where age <22
大于等于:
	db.集合名称.find({属性: {$gte: 数值}})
小于等于:
	db.集合名称.find({属性: {$lte: 数值}})
大于等于A并小于等于B:
	db.集合名称.find({属性: {$gte: A, $lte: B}})
```

- 特殊查询:

```sql
模糊:
db.集合名称.find({属性: /子字符串/})
    -- db.students.find({name: /cy/}) 相当于 select * from students where name like '%cy%'   -> 会查找到含name:lucy的对象 （%可以匹配任意个字符 包含0个字符）

    db.集合名称.find({属性: /^起始子字符串/})
        -- db.students.find({name: /^Luc/}) 相当于 select * from students where name like 'Luc%' 

查询指定属性（列）:
    db.集合名称.find({}, {属性: 1}) //或者 db.集合名称.find({}, {属性: true}) 多个属性时则 db.集合名称.find({}, {属性A: 1 , 属性B:1})
        -- db.students.find({}, {name: 1, age: 1})  相当于 select name, age from students   
        /* 
            此时检索的结果是  (只会检索到对象的指定属性)  如若是 db.集合名称.find({}, {属性: 0}) 或者 db.集合名称.find({}, {属性: false}) 则检索除了该属性外的所有属性
                {"name":Lucy,"age":20}
                {"name":Jack}
         */

    db.集合名称.find({属性: {$gt: 数值}}, {属性A: 1, 属性B: 1})
        -- db.students.find({age: {$gt: 20}}, {name: 1, age: 1} 相当于 select name, age from students where age >25

or查询：
	db.集合名称.find({$or: [{属性A: 数值}, {属性B: 数值}]})     // 注意这里的是数组类型
		-- db.students.find({$or: [{age: 22}, {age: 25}]}) 相当于 select * from students where age = 22 or age = 25

查询某个结果集的记录条数（统计数量 ）：
    db.集合名称.find().count()
        -- db.students.find({age: {$gte: 25}}).count() 相当于 select count(*) from students where age >= 25
		-- 如果要返回限制之后的记录数量，要使用 count(true)或者 count(非 0) => db.students.find().skip(10).limit(5).count(true); 
```

-----

##### 操作数据

```sql
修改数据:
	指定对象修改部分数据：
		db.students.update({"name":"Lucy"},{$set:{"age":33}})  -- 把students集合中name=Lucy的所有对象的age属性改成33
        
	指定对象修改对象成新对象：
		db.students.update({"name":"Lucy"},{"name":"Marry","age":33})  -- 把students原集合中name=Lucy的所有对象的内容变为{"name":"Marry","age":33}  id值不变

删除数据（集合里）：
	db.集合名称.remove()    --同理可以在remove()括号里指定删除条件 删除该集合里满足条件的所有数据
```

------

##### 返回操作后数据

格式：`findAndxxx`

如：update 和 findAndUpdate 的区别：

update 只是修改数据，如果需要返回修改后的结果，则用  findAndUpdate

----

##### 正则查找

有以下集合需要通过正则去查找

```json
{ "_id" : 100, "sku" : "abc123", "description" : "Single line description." }
{ "_id" : 101, "sku" : "abc789", "description" : "First line\nSecond line" }
{ "_id" : 102, "sku" : "xyz456", "description" : "Many spaces before     line" }
{ "_id" : 103, "sku" : "xyz789", "description" : "Multiple\nline description" }
```

可以直接使用正则来写

```javascript
db.collection.find({ sku: /adC/i })
```

也可以使用对象格式去写正则，属性为 `$regex` 和 `$options`

```javascript
db.collection.find({ sku: { $regex: 'abC', $options: 'i' }})
```

使用形参的写法

```javascript
function findData(field, query, options) {
    db.collection.find({ 
        field: { 
            $regex: new RegExp(query), 
        	$options: options 
        }
    })
}
```

option 规则：

- i：不区分大小写
- m：如果要查找的数据中有换行符（\n）且返回结果需要换行符，或者匹配的正则有使用到了锚（^ 和 $ 匹配头尾规则）
- s：允许点字符（.）匹配所有的字符，包括换行符
- x：忽略空白字符

```javascript
find({ description: { $regex: /^S/, $options: 'm' }});
     // 匹配结果：因为有换行符，因此 \n 后的S相当于是新的头，因此符合^S规则能被匹配
     { "_id" : 100, "sku" : "abc123", "description" : "Single line description." };
	 { "_id" : 101, "sku" : "abc789", "description" : "First line\nSecond line" };

find( { description: { $regex: /^S/} };
     // 匹配结果：
     { "_id" : 100, "sku" : "abc123", "description" : "Single line description." }
```

```javascript
find({ description: { $regex: /m.*line/, $options: 'si' }})
	// 匹配结果：. 可匹配换行符，因此不会受换行符影响
	{ "_id" : 102, "sku" : "xyz456", "description" : "Many spaces before     line" }
	{ "_id" : 103, "sku" : "xyz789", "description" : "Multiple\nline description" }

find( { description: { $regex: /m.*line/, $options: 'i' }})
	// 匹配结果：
	{ "_id" : 102, "sku" : "xyz456", "description" : "Many spaces before     line" }
```

-----

##### 查找中文

MongoDB Text Search 对中文支持不佳，因此往往都避免直接去搜索含有中文的关键字，如 `find({'姓名': '张三'})`，一般使用的是 `_id ` 作为搜索条件去搜寻，或者使用正则匹配

```
如当 query 为“你好”时，不匹配“你好张三"不匹配，匹配”你好，张三“
```

----

##### 错误码

错误处理的 `error` 中的 `code` 属性会提示为何种错误，可依据错误码弹出对应提示

- 11000：添加数据时，某个声明为唯一值的字段重复

```javascript
// 如一个注册用户场景,usr在schema中已声明 `unique: true`表示该字段属性值唯一
public async addUser(usr: string, psd: string) {
    try {
        const user = new User({usr, psd,})
        return await user.save()
    } catch(error) {
        if(error.code = 11000){     
            throw new Error('用户名已存在')
        }   else {
            throw error
        }
    } 
}
```







-----

#### 索引

```
索引是对数据库表中一列或多列的值进行排序的一种结构，可以让我们查询数据库变得更快
MongoDB 的索引几乎与传统的关系型数据库一模一样，这其中也包括一些基本的查 询优化技巧
为属性值添加索引 即搜索时不再遍历查找，而是只查找有该索引的属性值的各个对象，速度快很多
```

指令：

- 创建索引

```
db.集合名称.ensureIndex({"属性名":1})
为集合中对象的某个属性创建索引（至少拥有一个含该属性的对象才可创建索引） 1表示键的索引按升序存储 -1表示降序
```

```
复合索引
db.集合名称.ensureIndex({"属性名A":1,"属性名B":1})
如 db.user.ensureIndex({"username":1, "age":-1}) 
该索引被创建后，基于 username 和 age 的查询将会用到该索引，或者是基于 username 的查询也会用到该索引，但是只是基于 age 的查询将不会用到该复合索引
因此可以说， 如果想用到复合索引，必须在查询条件中包含复合索引中的前 N 个索引列

然而如果查询条件中的键值顺序和复合索引中的创建顺序不一致的话，MongoDB 可以智能的帮助调整该顺序，以便使复合索引可以为查询所用
如：db.user.find({"age": 30, "username": "stephen"}) 
对于上面示例中的查询条件，MongoDB 在检索之前将会动态的调整查询条件文档的顺序，以使该查询可以用到刚刚创建的复合索引
```

对于上面创建的索引，MongoDB 都会根据索引的 keyname 和索引方向为新创建的索引，自动分配一个索引名

- 创建建索引时为其指定索引名

```
db.user.ensureIndex({"属性名":1},{"name":"索引名"})
若之前存在索引名，则不能再指定

db.user.ensureIndex({"country":1,"age":-1},{"name":"Data"})
添加结果：
{
	"v" : 2,
	"key" : {
		"country" : 1, 
		"age" : -1  
	},
	"name" : "Data",        // 索引名 若不在创建时指定的话则指定创建
    "ns" : "for.user"    	// fordata是集合名称 即ns为 for.索引所在的集合名称
}
```

- 获取当前集合的索引

```
db.集合名称.getIndexes() 

查询结果如下（多个索引间用大括号和逗号隔开，全部索引包含在中括号中）
[
	{
		"v" : 2,
		"key" : {
			"_id" : 1   
		},
        "name" : "_id_",        	// 索引名 若不在创建时指定的话则指定创建
        "ns" : "for.fordata"    	// fordata是集合名称 即ns为 for.索引所在的集合名称
        },

	{
		"v" : 2,
		"key" : {
			"name" : 1      		// "为属性值设置的索引" 即 db.fordata.ensureIndex({"name":1})
		},
		"name" : "name_1",
        "ns" : "for.fordata"
    }
]
```

- 删除索引

```
db.集合名字.dropIndex({"属性名":1})
注意后面没有es
```

**唯一索引**

设置唯一索引的属性，它的属性值只能是唯一的 

如"age"属性设置了唯一索引 当集合中拥有一个 "age":5 的对象时 不能再插入另外一个含 "age":5 的对象 但是 "age":6 可以（非5）

- 创建唯一索引

```
db.集合名.ensureIndex({"属性名":1},{"unique":true})

db.students.ensureIndex({"sex":1},{"unique":true})
{
	"v" : 2,
	"unique" : true,
	"key" : {
		"sex" : 1
	},
	"name" : "sex_1",
	ns" : "for.students"
}
```

```
复合唯一索引：不满足任意一条属性值相同则可以插入对象
```

```
如果在为已有数据的文档创建索引时，可以执行下面的命令，以使 MongoDB 在后台创建索引，这样的创建时就不会阻塞其他操作
但是相比而言，以阻塞方式创建索引，会使整个创建过程效率更高，但是在创建时 MongoDB 将无法接收其他的操作
db.集合名.ensureIndex({"属性名":1},{"background":true}) 
```

**explain**

explain 会返回查询使用的索引情况，耗时和扫描文档数的统计信息

- 查询具体的数据查询时间

```
db.集合名.find().explain( "executionStats" )

关注输出的如下数值：explain.executionStats.executionTimeMillis  单位：毫秒
```

-----

### ----------------

------

### MySQL

#### 建库建表

```mysql
show databases;     				-- 查找当前所有数据库

use databasesName;     				-- 进入databaseName数据库

show tables;       					-- 查找当前数据库的所有表

show tables from databasesName;     -- 查找databasesName数据库里的所有表

create table tableName ()       	-- 在当前数据库下创建名为tableName的表 表格列内容可在括号里设置 如 CREATE TABLE author(authorID VARCHAR(20) NOT NULL);

desc tableName;     				-- 查看当前数据库下tableName的表结构
```

#### 查询相关

##### 一般查询

```mysql
SELECT prod_id FROM products;   						-- 从表products中检索prod_id列

SELECT prod_id,prod_name FROM products;    				-- 从表products中检索prod_id和prod_name列

SELECT *FROM products;      							-- 检索表product中的所有列

SELECT DISTINCT prod_id FROM products;      			-- 检索表products中不同的行，即如果prod_id里有多个相同的字段时，只会返回一个

SELECT prod_id FROM products LIMIT 5;       			-- 检索表products中的prod_id列，返回的行数不多于5个

SELECT prod_id FROM products LIMIT 5，10;       		   -- 检索表products中的prod_id列，返回的行数为第五行开始往后的十行

SELECT *FROM products ORDER BY prod_id;     			-- 检索表products的所有列，其顺序按prod_id的顺序排列

SELECT *FROM products ORDER BY prod_id,prod_name;       -- 检索表products中所有列，先按prod_id排列，当prod_id相同时按prod_name排列

SELECT *FROM products ORDER BY prod_id DESC LIMIT 1;    -- 检索表products中所有列，按prod_id由大到小排序(默认是由小到大)返回第一行
```

##### 条件查询

```
注意，sql中null表示什么也是不是，不能使用 =、<、>的所有判断，要判断时需要用is null 或者 is not null
```



```mysql
SELECT prod_id,prod_name FROM products WHERE prod_price = 2.50;     									-- 返回prod_price值为2.50对应的prod_id和prod_name行

SELECT prod_id,prod_name FROM products WHERE prod_price BETWEEN 5 AND 10;       						-- 返回prod_price在5到10范围内的prod_id和prod_name行

SELECT prod_id,prod_name FROM products WHERE prod_price IS NULL;        								-- 空值检查(prod_price = 0 而是 prod_price = NULL）
 
SELECT prod_id,prod_name FROM products WHERE prod_price > 10 AND prod_id = 12 AND prod_name = 'GO';     -- 检索满足全部条件的prod_id,prod_name 行

SELECT prod_id,prod_name FROM products WHERE prod_price > 10 OR prod_id = 12 OR prod_name = 'GO';     	-- 检索满足任意条件的prod_id,prod_name 行

SELECT prod_id,prod_name FROM products WHERE prod_price IN (2,3);       								-- IN与or作用一样 检索prod_price为2或3的对应~~列

SELECT prod_id,prod_name FROM products WHERE prod_price NOT IN (2,3);       							-- 匹配2和3以外的prod_price
```

##### 通配符

不要过度使用通配符 通配符很影响查询速度

```mysql
SELECT prod_id FROM products WHERE pard_name LIKE 'jet%';       -- %表示任何字符出现的任意次数，即检索pard_name 开头为jet的列 如jetaa jetbsa

SELECT prod_id FROM products WHERE pard_name LIKE '%jet%';      -- 匹配pard_name中含有jet的字符 如aajetaw oujetaarr(%也能匹配0个字符)    

SELECT prod_id FROM products WHERE pard_name LIKE '_jet';       -- _只匹配一个字符，不能多也不能少
```

##### 组合查询

UNION 将多条SELECT语句结合起来并返回一个结合起来的检索结果

```mysql
SELECT vend_id,prod_id,prod_price FROM products WHERE prod_price <= 5 UNION SELECT vend_id,prod_id.prod_price FROM products WHERE vend_id IN(1001,1002);
    -- 最终的检索结果不仅有prod_price <= 5的行，也会有 vend_id = 1001 OR 1002的行 
    -- UNION会自动合并相同条件的行，即如果有一行满足 prod_price <= 5 和 vend_id = 1001 OR 1002 则只会显示一行
    -- 只允许使用一次ORDER BY 且跟在最后一个SELECT最后
```

##### 正则查询

```mysql
SELECT prod_id FROM products WHERE pard_name REGEXP '.000';     			-- REGEXP代替LIKE 后面跟的东西为正则表达式，.表示任意匹配一个字符 例如即可以检索出JetPack 1000 或者 JetPack 2000

SELECT prod_id FROM products WHERE pard_name REGEXP '1000|2000|3000';       -- 与OR类似

SELECT prod_id FROM products WHERE pard_name REGEXP '[1,2,3]000';     		-- 与上面功能一样 匹配1000或2000或3000

SELECT prod_id FROM products WHERE pard_name REGEXP '[1-3]000';     		-- 范围匹配 匹配1000或2000或3000 也可以是[a-z]这种字母范围

SELECT prod_id FROM products WHERE pard_name REGEXP '\\.';      			-- \\匹配特殊字符 如检索到 'ABCD .87'   . | [] \ 都需要用\\来转义
```

**正则表达式规则：**

- 字符集

```
[:alum:]      任意字母和数字 同[a-zA-Z0-9]
[:alpha:]     任意字符 同[a-zA-Z]
[:blank:]     空格和制表 同[\\t]
[:cntrl:]     ASCII控制字符 ASCII 0到31和127
[:digit:]     任意数字 同[1-9]
[:graph:]     与[:print:]相同但不包括空格
[:lower:]     任意小写字母 同[a-z]
[:print:]     任意可打印字符
[:punct:]     既不在[:alnum:]又不在[:cntrl:]中的任意字符
[:space:]     包括空格在内的任意空白字符 同[\\f\\n\\r\\t\\v]
[:upper:]     任意大写字母 同[A-Z]           
[:xdigit:]    任意十六进制数字 同[a-fA-F0-9]
```

- 空白元字符

```
\\f       换页
\\n       换行
\\r       回车
\\t       制表
\\v       纵向制表
```

- 定位符

```
^     文本的开始      如^[0-9\\.]只在开头为.或任意数字时才匹配到  例如'9atm' '.ola11' 匹配不到[a1.2]这种
$     文本的结尾
[[:<:]]       词的开始
[[:>:]]       词的结尾
```

- 重复元字符

  以 `ABC_JACK_123_ABC` 为例

```
*         0个或多个匹配   'ABC'   匹配'ABC_JACK_123_ABC'
+         1个或多个匹配(等于{1,})   'ABC'   匹配'ABC_JACK_123_ABC'
?         0个或1个匹配(等于{0,1})     'ABC'   匹配不到 因为字符存在两个'ABC'
{n}       指定数目匹配    'ABC'   {2}     即可匹配到'ABC_JACK_123_ABC'
{n,}      不少于指定数目的匹配
{n,m}     匹配数目的范围(m不超过255)
```

```mysql
SELECT prod_id FROM products WHERE pard_name REGEXP '[[:digit]:]{4}';       -- 表示可以匹配到连在一起的4个数字，如'jet 4000'，'jet 2900'等 
```

---

#### 操作数据库相关

##### 拼接

```mysql
SELECT CONCAT(vend_name,'(',vend_country,')') FROM vendors;     				-- 把vendors表中的vend_name、(、vend_country、)字段拼接在一起 如~name里 ACME字段行对应~country的USA 则拼接后会形成一个新列 对应行为 'ACME(USA)'

SELECT CONCAT(RTrim(vend_name),'(',RTrim(vend_country),')') FROM vendors;       -- RTrim 去掉数据右侧多余空格    还有 LTrim 去左边空格   Trim 去两边空格

SELECT CONCAT(vend_name,'(',vend_country,')') AS vend_title FROM vendors;       -- 为拼接后形成的新列起别名为vend_title
```

##### 常用函数

- 文本处理函数

```
Left()            返回字符串左边的字符
Length()          返回字符串长度
Locate()          找出字符串的一个子串
Lower()           将字符串全部转为小写
Upper()           将字符串全部转为大写
Soundex()         返回字符串的SOUNDEX值       对字母音发音比较而不是文本 相近的模糊音会被找出 如lee 和lie
SubString()       返回子串的字符
```

- 日期和时间处理函数

```
AddDate()         --增加一个日期(天、周等)
AddTime()         --增加一个时间(时、分等)
CurDate()         --返回当前日期
CurTime()         --返回当前时间
Date()            --返回日期时间的日期部分
DateDiff()        --计算两个日期之差
Date_Add()        --高度灵活的日期运算函数
Date_Format()     --返回一个格式化的日期或时间串
Day()             --返回一个日期的天数部分
DayOfWeek()       --对于一个日期，返回对于的星期几
Hour()            --返回一个时间的小时部分
Minute()          --返回一个时间的分钟部分
Now()             --返回当前日期和时间
Second()          --返回一个时间的秒部分
Time()            --返回一个日期时间的时间部分
Year()            --返回一个日期的年份部分
```

```mysql
SELECT cust_id FROM orders WHERE Year(order_date) = 2005 AND Month(order_date) = 9;     -- 检索2005.9月的订单id
```

- 常用数值处理函数

```
Abs()     返回一个数的绝对值
Cos()     返回一个角度的余弦
Exp()     返回一个数的指数值
Mod()     返回除操作的余数
Pi()      返回圆周率
Rand()    返回一个随机数
Sin()     返回一个角度的正弦
Sqrt()    返回一个数的平方根
Tan()     返回一个角度的正切
```

```mysql
SELECT AVG(prod_price) AS avg_price FROM products;      		-- 返回prod_price(所有行加起来)平均数

SELECT COUNT(*) AS avg_price FROM products;    					-- 返回表prodicts中的总行数   使用COUNT(*)不会忽略值为NULL的行 而COUNT(cust_email)会对表products中cust_email列返回有值得总行数    

SELECT MAX(prod_price) AS max_price FROM products;     			-- 返回最大值(允许返回字符串，忽略NULL值)

SELECT MIN(prod_price) AS min_price FROM products;     			-- 返回最小值

SELECT SUM(prod_price) FROM orderitems WHERE order_id = 100;    -- 返回表中order_id = 100 对于的prod_price行的值的总和
```



-----

#### 数据规划

##### 分组

分组允许把数据分成多个逻辑组，以便对每个组进行聚集计算

```mysql
SELECT vend_id,COUNT(*) AS num_prods FROM products GROUP BY vend_id;
    /*
        1.检索vend_id列，计算该表总行并在新列num_prods中显示
        2.将检索到的两列以vend_id分组(即vend_id每一个不同值为一组)
            即结果为：
                vend_id  |  num_prods
                1001     |  3
                1002     |  2
                1003     |  7
                1004     |  8

        GROUP BY子句在WHERE子句后ORDER BY子句前
        如果分组列中具有NULL值，则NULL将作为一个分组返回，如果列中有多个NULL值，他们将分为一组
    */
```

##### 过滤

```mysql
SELECT vend_id,COUNT(*) AS num_prods FROM products GROUP BY vend_id HAVING COUNT(*) >= 7;
    /*
        将vend_id组中，行数>=7的组留下，其他组去掉
        结果为：
            vend_id  |  num_prods
            1003     |  7
            1004     |  8

        HAVING作用和WHERE类似 但是HAVING过滤的是分组，而WHERE过滤的是行(WHERE在分组前进行过滤，HAVING在分组后过滤 WHERE、HAVING可以同时用)

        SELECT语句顺序:
            SELECT
            FROM
            WHERE
            GROUP BY
            HAVING
            ORDER BY
            LIMIT
    */
```

##### 联结

联结可以让不同表存放着各种的信息，只需要用一列对应别可以将他们的信息对应起来，方便数据分类管理

- 使用 `WHERE`

```mysql
SELECT vend_name,prod_price FROM vendors,products WHERE vdendors.vend.id = products.vend_id;
        /*
            存在两张表，vend_name 和 prod_price 
            vendors表中有vend_name和vend_id两列     products表中有prod_price和vend_id两列
            两表中vend_id相同 为了让其vend_name和另外一个表中的prod_price一一对应
            则用相同的vend_id属性将两列的行对应起来
        */
        
SELECT vend_name,prod_price FROM vendors,products WHERE vdendors.vend.id = products.vend_id AND products.prod_price = 1.00;      --查prod_price值为1对应的vend_name
    
SELECT a.lineA,a.lineB,a.lineC, b.*from vendors a, products b WHERE a.vend.id = b.vend.id;  -- 别名并联结多列(很常用！！)
```

使用 `INNER JOIN`

```mysql
SELECT vend_name,prod_price FROM vendors INNER JOIN products ON vdendors.vend.id = products.vend_id; 
```

-----

#### 数据操控

```mysql
-- 数据插入(INSERT)
    INSERT INTO customers(
        cust_name,
        cust_city
        )
    VALUES(
        'LUCY',
        'NULL'
    );

-- 在表customers中的cust_name和cust_city分别插入两行 LUCY 和 NULL  一个值对应一个列 即使值为空也要补上NULL
        INSERT INTO customers(
        cust_name,
        cust_city
        )
    VALUES(
        'LUCY',
        'NULL'
    ),
        (
            'MARRY',
            'BEIJING'
    );
-- 连续在插入多行

-- 更新数据(UPDATE)
    UPDATE customers SET cust_email = 'elmer@fudd.com' WHERE cust_id = 1005;        --将cust_id = 1005 的 cust_email 列 对应行修改为指定信息

-- 删除数据(DELETE)
    DELETE FROM customers WHERE cust_id = 10006;        --删除表customers中cust)id=10006的行
```

