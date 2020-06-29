# ES6 语法

## 块级作用域

声明变量新增`let`、`const`关键字

`let`声明的变量，只在该变量所在代码块内有效

`const`声明一个不可修改只读的常量

PS: 不管懂不懂，用就是了

## 模板文字

使用反引号`` ` ``代替单引号/双引号包裹字符串

好处是，字符串可以换行，字符串里面可以`${vari}`这样使用变量


## 解构赋值

解构赋值的作用是可以快速取得数组或对象当中的元素或属性，而无需使用`arr[x]`或者`obj.key`等传统方式进行赋值

### 传统方式获取数组元素

```js
var arr = [1, 2, 3, 4, 5];

a = arr[0];
b = arr[1];
```

### 使用解构赋值

```js
var arr = [1, 2, 3, 4, 5];

var [a, b] = arr;   //a:1 b:2
var [c, , d] = arr;   //c:1 d:3
```

### 多维数组使用解构赋值

```js
var arr = [1, 2, [3.1, 3.2], 4, 5];

var [a, b, [c, d]] = arr;   //a:1 b:2 c:3.1 d:3.2
```

### 对象使用解构赋值

```js
var obj = {
    name: 'test',
    age: 25,
    son: {
        name: 'fff',
        age: 1
    }
};

//变量名与属性名一致的解构赋值
var {name, age, son} = obj;
console.log(name);  //name:'test'
console.log(age);   //age: 25
console.log(son);   //son: {name:'fff', age:1}

//变量名与属性名不一致的解构赋值
var {name: username, age: ageee, son: children} = obj;
console.log(username);  //username:'test'
console.log(ageee);     //ageee: 25
console.log(children);   //children: {name:'fff', age:1}
```

#### 对象使用解构赋值，还可以用于函数参数

```js
function test({ name }) {
    //可以直接使用传入参数对象中的值
    console.log(name);
}

let obj = {
    name: '1212',
    age: 111
}

test(obj);
```


## 扩展运算符

`...`在数组或对象之前加这三个点，会把数组对象里面的属性展开成一系列用逗号分隔开来的值

```js
var arr = [1,2,3];
var tmp = [...arr]; //tmp: [1,2,3]
```

## rest运算符

rest运算符也是三个点号，不过其功能与扩展运算符恰好相反，把逗号隔开的值序列组合成一个数组，用在函数参数不定长

```js
function test(val, ...args) {
    console.log(val);
    console.log(args);
}

//调用函数
// val: 1 args: [2,3,4]
test(1, 2, 3, 4);

```

## 箭头函数

箭头函数提供了一种更加简洁的函数书写方式。基本语法是：
```
参数 => 函数体
```

当箭头函数没有参数或者有多个参数，要用 `()` 括起来

当箭头函数函数体有多行语句，用 `{}` 包裹起来，表示代码块，当只有一行语句，并且需要返回结果时，可以省略 `{}`, 结果会自动返回

## 对象

### 对象字面量

ES6允许对象的属性直接写变量，这时候属性名是变量名，属性值是变量值

```js
const age = 12;
const name = "Amy";
const person = {age, name};
console.log(person)   //{age: 12, name: "Amy"}
```

**方法名也可以简写**

```js
const person = {
  sayHi(){
    console.log("Hi");
  }
}
person.sayHi();  //"Hi"
```

### 对象新方法

#### Object.assign(target, source_1, ···)

用于将源对象的所有可枚举属性复制到目标对象中

如果目标对象和源对象有同名属性，或者多个源对象有同名属性，则后面的属性会覆盖前面的属性

如果该函数只有一个参数，当参数为对象时，直接返回该对象；当参数不是对象时，会先将参数转为对象然后返回

assign 的属性拷贝是浅拷贝，拷贝的只是指针

#### Object.is(value1, value2)

用来比较两个值是否严格相等，与（===）基本类似


