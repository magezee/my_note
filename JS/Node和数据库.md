## MongoDB

需要引入`mongodb`模块

```javascript
var MongoClient = require('mongodb').MongoClient;
// 或
import mongoose from 'mongoose'
```

### 数据库操作

- **connect( )**

  连接数据库，对数据库的操作均需要在该方法中的回调函数里执行

```js
// 连接数据库时，有些弃用的功能需要声明，不然会弹出警告(对功能没影响)，如：
	useCreateIndex: true,
	useNewUrlParser: true,
	useUnifiedTopology: true,
	useFindAndModify: false,
```

```javascript
var MongoClient = require('mongodb').MongoClient;
var url = 'mongodb://127.0.0.1:27017/';
MongoClient.connect(url,{
    	useNewUrlParser:true, 
    	useUnifiedTopology:true
	},function(err,db){       
        if(err){
            console.log(err);
            console.log("数据连接失败");
        	return;
    })

var db1 = db.db('db1');     // 选择mongoDB库里的db1数据库	即 mongoDB（db） → mongoDB里的数据库 → 指定数据库里的集合 → 指定集合里的数据
}
```

使用 promise 的写法（推荐）

```js
import mongoose from 'mongoose'
var url = 'mongodb://127.0.0.1:27017/'
mongoose.connect(url, {
	useCreateIndex: true,
    useNewUrlParser: true,
    useUnifiedTopology: true,
    useFindAndModify: false,
})
.then(() => {
    return console.log('成功连接数据库')
})
.catch((error) => {
    console.log(`错误信息:${error}`)
    return process.exit(1)	// 结束该进程
})
```

- **connect( ).on( )**

​	声明数据库的某种状态发生时触发的函数

```js
// 连接成功
mongoose.connection.on('connected', function () {    
    ...
})

// 连接异常
mongoose.connection.on('error',function (err) {    
    ...
})

// 连接断开
mongoose.connection.on('disconnected', function () {    
    ...
}) 
```



-----

### Schema和Model

- 在 Mongoose 中，所有数据都由一个 Schema 开始创建
- 每一个 schema 都映射到一个 Mongodb 的集合(collection)，并定义了该集合(collection)中的文档(document)的形式
- shema 相当于定义表结构，model 定义表

**Schema**

可以认为，在非关系型数据的mongoDB中，设置 Schema 去规范一个集合应该存放的数据类型的格式

```
如：
对某个属性规定值的数据类型
对Number的数据设置最小值min与最大值max
对Date设置默认值为当前时间
对String设置存储为小写模式并去除头尾空白字符
```

定义 Scheme，`new Schema({})` 对象中的每一个属性称之为 Schema 的键

每一个键都定义了一个文档(document)的一个属性

```js
import { Schema } from 'mongoose'

const UserScehma = new Schema({
  name: { type: String, required: true },
  createTime: { type: Date, default: Date.now },
  favoriteIds: [String],
  sex: String,
  avatar: String,
  vip: Boolean,
})
```

键值必须声明类型，如果想要其他功能时，则声明为一个对象

```
type
	规定该数据的数据类型
required
	规定创建文档(document)时该属性必须设置
	如 required: true 规定创建文档时name 必须设置
default
	如果没设置则默认为指定的值
	如 createTime 会被映射为 Date 的 Schema 类型，如没设置默认为Date.now 的值
```

允许的Schema类型

```
String
Number
Date
Buffer
Boolean
Mixed
ObjectId
Array
```

可以通过 api 来对 Schema 的动态操作

```js
UserScehma.add({money : Number})	// 增加一个键
```

**创建Model**

在Mongoose的设计理念中，`Schema `用来也只用来定义数据结构，具体对数据的增删改查操作都由 `Model` 来执行

可以用 `mongoose.model(modelName, schema)` 将定义好的 `phoneSchema` 转换为 `Model`

```js
import { Schema, model } from 'mongoose'

var phoneSchema = new Schema({
	device 		: String,    //设备名称
	isSmart 	: Boolean,   //是否为智能手机
	releaseTime	: Date,      //发布时间 
	price		: Number,    //售价
	apps		: [{name : String}], //手机中安装的App名称,是数组
	manufacturer: {         //手机厂商
		name 	: String,   //厂商名称
		country	: String    //厂商国籍
	}
})

const Animal = model('Animal', animalSchema)
```

**创建数据实例**

`Model` 相当于数据的构造函数，可用 `new`实例化出一个数据对象实例，构造函数的参数为一个对象，属性为插入一条数据具体的数值

```js
var iPhoneSE = new Phone({
	  device    : "iPhone SE",
	  isSmart   : "true",
	  releaseTime : "2016-03-21 10:00:00",
	  price   : 4999,
	  apps    : [{name : "Safari"}, {name : "Map"}, {name : "Tinder"}],
	  manufacturer: {
	    name  : "Apple",
	    country" : "The United States"
	  }
})
```

也可以省略属性名直接填属性值，但是这样得要求按声明顺序

```javascript
var iPhoneSE = new Phone({
	  "iPhone SE",
	  "true",
	  "2016-03-21 10:00:00",
	  4999,
	  [{name : "Safari"}, {name : "Map"}, {name : "Tinder"}],
	  {name  : "Apple", country" : "The United States"}
})
```

**Mondel实例方法**

```
在赋值 Schema 的方法时，不能使用箭头函数，否则会造成上下文绑定的问题！下同
声明方法时可用 async function 来声明异步操作
```

将自定义的方法添加到`Schema.methods`，这样通过该 Schema 转化而来的 Model 创建出来的每一个实例都能拥有这个方法（注意是在 Scheam处添加而非 Mondel）

实例方法关心的是具体某一数据的值，如一个数据对象有很多属性和值，只关心其中一条

```js
import { Schema, model } from 'mongoose'
const peopleScheam = new Scheam({name: String, sex: String})
const People = model('People', peopleScheam)

// 添加实例方法
peopleScheam.methods.say = function() {
    console.log(this.name)		// 只想知道该数据的name值而不要其他属性值
}

// Mondel实例使用实例方法
const jack = new People({name: 'Jack', sex: 'boy'})
jack.say()
```

Model实例方法只能被Model的实例调用，不能被Model直接调用，否则会抛出`is not a function`异常，相当于类不能直接调用自己的实例方法，只能调用静态方法

**Model静态方法**

将自定义的方法添加到`Schema.statics` 就是静态方法

静态方法关心的是整个集合的某种数据（如集合中存储了多少条数据）

```js
import { Schema, model } from 'mongoose'
const peopleScheam = new Scheam({name: String, sex: String})
const People = model('People', peopleScheam)

// 添加静态方法1
peopleScheam.statics.printCount = function() {
    this.count({},(err, count) => {
        if(err) {
            console.log(err)
        } else {
            console.log(count)
        }
        
    })
}

// 添加静态方法2
peopleScheam.statics.findByName = async function(name) {
    return this.findOne({name})
}


// Mondel使用静态方法
People.PrintCount()

People.findByName('jack').then(people => {
    console.log(people.sex)		// 通过条件jack查到指定的数据对象，再打印对象中的sex属性
})
```

静态方法中，this 指向的是一个集合，因此可以用 this 去使用集合的方法，如查询语句 `db.集合名称.find()` 可以写成 `this.find()`

**查询助手**

将自定义的方法添加到`Schema.query` 

一般用于根据指定条件查找到某一数据后，再对查找到的数据进行额外处理

```js
import { Schema, model } from 'mongoose'
const peopleScheam = new Scheam({name: String, sex: String})
const People = model('People', peopleScheam)

// 添加实例方法
peopleScheam.query.byName = async function(name) {
    return this.find({name})
}

// 使用查询助手
People.find().byName('jack').exec().then(people => {
    console.log(people.sex)		
})
```

查询助手的使用和静态方法类似，但是必须要先使用 `.find()` ，同时结尾必须进行 `.exec()`

**索引**

mongodb 中每一条数据（document）都有一个 `_id`的 主键，即第一索引，可以自定义第二索引（独立、复合索引）

- 创建独立索引

  在定义 Schema 时创建，也可用 `Schema.index` 创建

  根据单个属性查找数据时使用

```js
import { Schema, model } from 'mongoose
const peopleScheam = new Scheam({
    name: {type: String, index: true}	// 为 name 创建独立索引 
    sex: {type: String, index: true}	// 为 sex 创建独立索引 
})

// peopleScheam.index({ name: 1})
// peopleScheam.index({ sex: -1})
// 不要 peopleScheam.index({ name: 1, sex:-1})，这样创建出来的是复合索引
```

- 创建复合索引

  用 `Schema.index` 创建

```js
peopleScheam.index({ name: 1, sex:-1})
```

- 声明该索引为唯一索引

  用 `Schema.index` 创建，且声明 `{unique:true}`

```js
peopleScheam.index({ name: 1}, {unique:true})		// 为 name 创建独立唯一索引
peopleScheam.index({ name: 1, sex:-1}, {unique:true})	// 创建组合唯一索引
```

**document**

如果是用ts写的，则可以用document来写接口拿到documen的类型，对model进行类型规范

```ts
import { Schema, model, Document } from 'mongoose'

// 声明接口继承Document规范对象类型
interface ITodo extends Document {
  content: string;
  status: boolean;
}

const TodoSchema: Schema = new Schema({
  content: String,
  status: {
    type: Boolean,
    default: false,
  },
})

model<ITodo>("Todo", TodoSchema)
```



---

### 常用方法

```
现在一般不用：数据库名字.collection('集合名字').xxx方法
而是创建 Schema 和 Model 再通过Model去调用方法
```

直接使用Model调用集合的方法，使用Model创建的具体数据调用具体实例方法

```js
const peopleScheam = new Scheam({name: String, sex: String});
const People = model('People', peopleScheam);

People.find();	// 调用集合方法

const people = new People({name:'jack', sex:'男'});
people.save()	// 调用具体数据的实例方法
```

**原生方法**

- **insertOne ( )**

  在指定数据库的指定集合中插入一条数据

```javascript
db1.collection('students').insertOne({      // 选择 db1 数据库中的 students 集合 插入一条数据
        "name":"Bens",
        "sex":"男"
    },function(error,result){
        if(error){
            console.log(error);
            console.log("数据增加失败");
            return;
        }
        console.log("增加数据成功");
        db.close();     // 关闭数据库 每次打开数据库后都需要关闭
}); 
```

- **updateOne ( )**

  在指定数据库的指定集合中修改指定数据

```javascript
db1.collection('students').updateOne({"name":"Jack"},{$set:{"country":"CN"}},function(error,result){    // 如果在数据库里找不到对应的对象，不会进入到error操作
	if(error){
		console.log(error);
		console.log("修改数据失败");
		return;
    }
    console.log("修改数据成功");
	db.close();
})
```

- **deleteOne ( )**

  在指定数据的指定集合中删除指定数据

```javascript
db1.collection('students').deleteOne({"name":"Marisue"},function(error,data){       // xx one 只对检索到的第一条数据操作 如本来存在三条 "name":"Marisue" 的数据 只删除一条 还剩下两条
	if(error){
		console.log(error);
        console.log("数据删除失败");
        return;
    }
	console.log("数据删除成功");
	db.close();
})
```

- **find ( )**

  在指定数据的指定集合中查询指定数据

```javascript
var result = db1.collection('students').find({});       // 查询到的数据  注意：如果conlose.log(result) 会显示一大堆无用数据
    var list = [];      // 用于存放数据的数值
    result.forEach(function(error,doc){
        if(error){
            console.log(error);
        }
        else{
            if(doc != null){        // foreach 依次读取数组中的数据，如果读完所用数据，则会返回null
                list.push(doc);     // 为数据添加读取到的数据
            }
            else{       // 为 null 时表示数据已读取并存储完成 
                console.log(list);
            }
        }
})
```



**实例方法**

- **save()**

  将该条数据存储在集合中

  与 `insert` 不同之处在于：当用 `insert` 插入数据时，如果集合中已存在唯一主键（_id）一样的数据，则会报错，但是用 `save` 则会修改并覆盖原数据

  如果不指定 `_id` 插入数据的话两种方式是没有区别的（不指定_id时，会自动生成）

```
如果想通过主键查找某一数据，再对数据进行修改并存储时，应该使用 save
```

```javascript
import { Schema, model } from 'mongoose'
const peopleScheam = new Scheam({name: String, sex: String})
const People = model('People', peopleScheam)

const jack = new People({name: 'Jack', sex: 'boy'})
jack.save()
```



**静态方法**





----

### 联结与populate

```
写的不错的博客：https://itbilu.com/nodejs/npm/HkAKMTECm.html
```

Mongoose 可进行集合间的联结（和mysql的表联结类似）

type 的类型为 `Schema.Types.ObjectId` （也可以简写为 ObjectId ）

ref 指向联结的集合，注意该集合必须是注册过 model 的

```javascript
import { Schema, model } from 'mongoose'

const petScheam = new Scheam({
    name: String,
    type: String
})
const Pet = model('Pet', petScheam)

// 将pet与people联结
const peopleScheam = new Scheam({
    name: String, 
    sex: String,
    pet: {
        name: string,
        type: Schema.Types.ObjectId,
        ref: 'Pet'
        
    }
})
const People = model('People', peopleScheam)
```

具体的联结的是哪条 pet 数据是靠主键来判断的，通过向 People 的 pet 属性添加某个 pet 数据的主键，由此来对进行数据绑定 

```javascript
const coco = new Pet({name: 'coco', type: 'cat'})
const jack = new People({
    name: 'jack',
    sex: 'boy',
    pet: coco._id	// 存储对应数据主键
})
```

如果有多条数据，那么应该使用数组，把多条联结数据的 `_id` push 到这个数组中就行

```javascript
const peopleScheam = new Scheam({
    name: String, 
    sex: String,
    pet: [{
        name: string,
        type: Schema.Types.ObjectId,
        ref: 'Pet'
        
    }]
})
```

但是取数据时如果直接取则不能取到正常的数据，只能取到某条 pet 数据的 `ObjectId`，如果想用联结的数据对象去填充 `objectId`，应该使用 `populate`，注意，即使在数组里面，populate仍能将数组中所有的`objectId`填充进去，很方便

```javascript
People.findOne({name: 'jack'})
	.populate('pet')	// 查找一条name为jack的people数据，将该数据连接的pet数据填充到people的pet属性
	.exec(function (err, doc) {
    	if(err) {
            throw new Error(err)
        }
    	return doc
	})

/* 
	如果不用populate： People.findOne({name: 'jack'})
    返回的数据为
        {
            _id: ObjectId("584a030733604a156a4f65ff"),
            name: 'jack',
            sex: 'boy',
            pet: ObjectId("584a030733604a156a4f6601")
        }

	使用populate返回数据为
		{
            _id: ObjectId("584a030733604a156a4f65ff"),
            name: 'jack',
            sex: 'boy',
            pet: {
            	name: 'coco',
            	type: 'cat'
            }
        }
	
*/
```

只填充联结的数据的某字段：比如上面代码如果只想知道 pet 的 `type` 而对 `name` 不关心，那么可以指定返回的数据属性，将所需返回的属性名当第二个参数传递给 `popultae` 即可

```javascript
People.findOne({name: 'jack'}).populate('pet', 'type').exec()	

/* 
	{
		_id: ObjectId("584a030733604a156a4f65ff"),
		name: 'jack',
		sex: 'boy',
		pet: {
			name: 'coco',
			type: 'cat'
		}
	}

*/
```

如果想填充对象数组里的某个属性字段，则直接在polulate的参数里进行设置

```js
var User = new Schema({
  username: {type: String},    
  password: {type: String},
  typeQuestion: [{q: {type: Schema.Types.ObjectId, ref: 'Question'}, a: String,option:String }],    
});

User.find({})
    .populate('typeQuestion.q')		// 填充q属性
    .exec(function(err, user) {
        // callback
    });
```

填充多个联结属性：只需要链式多次调用`populate()`方法即可

```javascript
import { Schema, model } from 'mongoose'

// 定义schema和model
const petScheam = new Scheam({
    name: String,
    type: String
})
const Pet = model('Pet', petScheam)


const PhoneScheam = new Scheam({
    number: number
})
const Phone = model('Phone', PhoneScheam)


// 和Pet、Phone联结
const peopleScheam = new Scheam({
    name: String, 
    sex: String,
    phone: {
        type: Schema.Types.ObjectId,
        ref: 'Phone'
    },
    pet: {
        type: Schema.Types.ObjectId,
        ref: 'Pet'
    }
})
const People = model('People', peopleScheam)
```

```js
People.findOne({name: 'jack'})
    .populate('pet', 'type')	// 填充对象中的pet和type属性
    .populate('phone')			// 填充对象中的phone属性
	.exec()
```

如果不是链式调用，则只会生效最后一个

```javascript
People.findOne({name: 'jack'})
    .populate('pet', 'type')	
    populate('phone')
```

populate的条件查询

```markdown
populate(path, [select], [model], [match], [options])，[]表示可选
	path: 		指定要填充的关联字段
	select: 	指定填充 document 中满足条件的字段
	model		指定关联字段的 model，如果没有指定就会使用Schema的ref(一般在ref声明就好了)
	match		指定附加的查询条件
	options		指定附加的其他查询选项，如排序以及条数限制等等
```

```javascript
People.find({name: 'jack'}).populate({
    path: 'Pet',
    match: { type: 'cat'},	// 假设jack关联了很多条pet数据，只想取得 type 为 cat 的数据
    select: 'name',			// 只填充 pet 的 name 字段
    options: { limit: 5 }	// 最多返回五条数据
})
```

```js
// 假设Author的articles属性和Article联结
author = await Author.findOne({authorName: ctx.session?.username}).populate({
	path: 'articles',
	options: {
		skip: 1,
		limit: 5,
		sort: {articleTime: 1}
	}
}); 
articles = author?.articles

// 等同于
articles = await Article.find().skip(1).limit(5).sort({articleTime: 1});
```



----

### exec和then

```js
model.find().then()
model.find().exec()
```

两者返回的都是 `promise` 对象

mongoose  的所有查询操作返回的结果都是 `query` , 并非一个完整的 `promise`，加上 `.exec`  或 `.then`将会返回成为一个完整的 promise 对象

- exec 一般用于独立的动作一次性执行

  参数为 `callback(err, doc)`

```js
model.find().exec((err, doc) => {
    if(err){
        console.log(err)
    }
    console.log('查询到的数据为:', doc)
})
```

- then 一般用于连续性的动作

  用法就是普通的 promise 对象的 then 用法

```js
// 也可以同时用
model.find().exec().then()
```





----

### 连续操作数据

当执行一连串的数据操作时（比如通过某条件查找某条数据，再对数据进行操作），由于是对数据库的操作是异步的，如果要保证执行顺序，应该使用 `promise` 或 `async function`

如先拿到 A数据，再根据A数据的属性对B数据进行修改

```javascript
import { Schema, model } from 'mongoose'
const peopleScheam = new Scheam({name: String, home: String})
const People = model('People', peopleScheam)

function removePeople(name) {
	People.find({name: 'jack'})
        .then(res => {
			People.update({name: 'jim'},{$set: {home: res.home}})
    	})
    	.catch(err => {
        	throw new Error(err)
    	})
}
```

```javascript
import { Schema, model } from 'mongoose'
const peopleScheam = new Scheam({name: String, home: String})
const People = model('People', peopleScheam)

async function removePeople(name) {
	try{
        const res = await People.find({name: 'jack'})
        await People.update({name: 'jim'},{$set: {home: res.home}})
    } catch(err) {
        throw new Error(err)
    }
}
```



-----

### 错误处理

对数据库的操作往往需要拥有错误处理机制

方法一：使用 exec

```js
import { Schema, model } from 'mongoose'
const peopleScheam = new Scheam({name: String, sex: String})
const People = model('People', peopleScheam)

function addPeople(name, sex) {
    const people = new People({name: name, sex: sex})
    people.save().exec((err, doc) => {
        if(err){
            throw new Error('新增失败')
        }
    })
}
```

方法二：使用 `try/catch` （推荐）

```javascript
import { Schema, model } from 'mongoose'
const peopleScheam = new Scheam({name: String, sex: String})
const People = model('People', peopleScheam)

function addPeople(name, sex) {
    const people = new People({name: name, sex: sex})
   	try {
        people.save()
    } catch(err) {
        throw new Error('新增失败')
    }
}
```



----

## MySQL

需要引入`mysql`包

```javascript
const mysql = require('mysql')
```

**使用：**

- mysql.createConnection ( )

  创建连接，返回所连接的数据库引用

```javascript
const mysql = require('mysql')、

const db = {
    host: "localhost",
    prot: 3306,
    user: "root",
    password: "123456",
    database: "blog"    // use blog
}

const database = mysql.createConnection({
    host: db.host,
    prot: db.prot,
    user: db.user,
    password: db.password,
    database: db.database
})

database.connect();		// 连接数据库
```

- mysql.query ( )

  执行数据库指令

```javascript
var queryCount = 'SELECT COUNT(*) AS articleNum FROM article';	// 直接输入sql命令就行

mysql.query(queryArticle,function(err,rows,fields){		// rows就是执行命令后返回的数据
	if(err){
      console.log(err);
      return;
    }
var articles = rows;    // 拿到数据
```

