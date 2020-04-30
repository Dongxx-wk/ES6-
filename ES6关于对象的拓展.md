### ES6关于对象的拓展 

##### （1）对象属性方法的简洁书写（感觉挺麻烦）

允许直接写入**变量**和**函数**作为对象的**属性**和**方法**：

变量作为对象的属性：

```js
var a='wangke';
var b={a}  // {a:'wangke'};
//等同于
var b={a:a};
```

函数作为对象的方法：

```js
var a={
    b:function(){
        console.log("wangke");
    }
    //等同于
    b(){
        console.log("wangke");
    }
}
```

##### (2) 对象属性名表达式

属性用表达式一般用[],但是ES5里对象字面量里不能有[],ES6里有

```js
var a="wangke";
var b={
    [a]:"Dongxx" //"wangke":"Dongxx"
    ["wang"+"ke"]:"Dongxx" //同上
    [a](){
        //方法
    }
	//如果a是一个对象 会转换为字符串[object Object] 一个小 一个大
	[a]:"Dongxx" //[object Object]:"Dongxx"
}
```

##### (3)对象方法的name属性

普通的同函数一样

bind方法创建的函数返回"bound",

Function 构造函数创建的返回“anonymous”，

```js
//getter 和 setter
var a={
    get b(){},
    set c(x){}
};
a.get.name //"get b"
a.set.name //"set c"
var key1=new Symbol('wangke');
//Symbol
var object={
    [key1](){}
}
object[key1].name//wangke
```

##### (4)Object.is()

作用相当于”===“，

弥补了“===”的缺点，即NaN不等于NaN，而-0等于0

而“==”的缺点是会强制类型转换

```js
Object.is({},{}) //false
```

##### (5)Object.assign()

Object.assign(目标对象，源对象，源对象......)

改变目标对象，源对象的内容复制带目标对象，返回值跟目标对象一样。

只有目标对象，返回目标对象

目标对象不是对象，会转变为对象，如果是null，undefined会报错 

源对象不是对象，字符串会转变为对象，数值和布尔不会转换，因为字符串对象可以有可枚举的属性，如果是null，undefined，会忽略

源对象的属性是不可枚举的时候，就不能复制（enumerable:false)

属性名为Symbol的值，也会被复制

注意：实行的浅复制，如果源对象的属性里对应的又是对象，那么复制的即是对这个对象的引用

复制的内容只关注第一层，第二层部分相同，第二层也会被全部取代

##### （6）属性的可枚举性

每一个对象的每一个属性都会有一个描述对象Descriptor

```js
var a={b:'wangke'};
Object.getOwnPropertyDescriptor(a,'b')
//
{
    enumerable:true,
    value:'wangke',
    writable:true,
    configurable:true
}
```

跟enumerable：false有关的枚举：(都不包括)

for   in .....  枚举对象自身和继承的可枚举属性

Object.keys()  返回对象自身的可枚举的属性的数组

JSON.stringify() 串行化对象自身可枚举的属性

尽量不要用for  in 而是用Object.keys()
##### （7） 属性的遍历
for   in   （不含Symbol属性）

Object.keys(obj)  (不含Symbol属性)

Object.getOwnPropertyName(obj) 返回对象的自身属性和继承属性的数组（不含Symbol属性，但是包含不可枚举的属性）

Object.getOwnPropertySymbols(obj) 返回对象的Symbol属性的数组

Reflect.ownKeys(obj) 返回对象的所有属性 不管是不是不可枚举的还是Symbol属性

以上五种方法遍历的次序规则：
先排数字 按顺序大小  再排字符串  按先来后到  再排Symbol属性 按先来后到

##### （8）
__proto__  用来读取或者设置对象的prototype对象，但是最后不要用，不是一个正式的Api，而是单纯的浏览器支持
obj.__proto__

```js
Object.setPrototypeOf({},null);//返回值为参数数组本身，发生了改变
var a={x:1};
var b={};
Object.setPrototypeOf(a,b);
b.y=2;
b.z=3;
a.y//2
a.z//3
```
第一个参数如果不是对象会转变为对象，但是返回值还是本身，因此没有意义，如果是null，undefined，会报错

Object.getPrototypeOf(obj) 读取对象的prototype对象 参数不是对象自动转化为对象  如果是null，undefined，则会报错

##### (9)Object.keys(obj),Object.values(obj),Object.entries(obj)(注意和reflect.ownEntries的区别）
配合for  （let  i  of  ）  使用
obj不是对象的强制转换成对象  Objec.values(obj)  里面是数字或者布尔时 返回空数组[]

##### （10） 对象的拓展运算符 ...
跟数组相似 
只能放在最后面  右边得是一个对象 是null或者undefined就会报错  
解构赋值是浅复制 如果复制的是一个对象的对象 则要小心啦
解构赋值可以赋值继承的属性，但是双重解构赋值（解构赋值+拓展运算符），则只会复制自身的属性
可以把...x  x看作是一个对象
``` js
let x={a:1,b:2};
let y={...x};//相当于let y=Object.assign({},x)
```
复制整个对象obj的两种方法：
```js
//写法一
var obj1={
__proto__:Object.getPrototypeOf(obj),
...obj
}
//写法二
var obj2=Object.assign({},Object.create(Object.getPrototypeOf(obj)),obj);
```
最好用写法二   写法一__proto__属性在非浏览器环境下不一定部署
var a={...x,{y:1,z:2}}  这种可以  功能是改某些值

var a={...null,...undefined} 不报错, ...后面也可以跟表达式

##### (11) Object.getOwnPropertyDescriptors(obj)
解决Object.assign无法复制get属性和set属性 
```js
//Object.getOwnPropertyDescriptors(obj)和Object.defineProperties
var a={
set b(value){
console.log(value);
}
}
c={};
Object.defineProperties(c,Object.getOwnPropertyDescriptors(a));
Object.getOwnPropertyDescriptor(c,'b');
```
另一个功能是实现某一个对象的克隆，即原型对象和自身的描述对象的合并
```js
//Object.create() Object.getOwnPropertyDescriptors()
var a=Object.create(Object.getPrototypeOf(obj),Object.getOwnPropertyDescriptors(obj))
```
##### (12) Null传导运算符
a?.b?.c||'default'
判断a有没有，然后判断a.b有没有，然后判断a.b.c或者default
a或者a.b其中一个返回null或者undefined，结果返回undefined



