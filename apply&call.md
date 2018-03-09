# apply 与 call 方法使用说明

**apply与call的作用与区别**

参数说明：  
共同点：第一个参数是为了改变构造函数的this指向  
a. apply方法第二个参数为数组或伪数组类型，方法内会进行遍历  
b. call方法除第一个参数外的参数可视为数组或伪数组遍历后出来的结果，以','分隔开  
作用：简单来说就是继承其他构造函数实例化后对象的属性，两者作用相同  
区别：两者的参数列表不一样  
ps: 一般在什么情况下都能用apply方法  

**apply示例**
```
function Test_fn(name, age) {
  this.name = name;
  this.age = age;
}

function Use_apply(name, age, sex) {
  var _this = this; // Use_apply构造函数的this指针，用于apply方法的第一个参数

  Test_fn.apply(_this, arguments); // 使用apply方法改变Test_fn构造函数内的this指向，内部会对arguments进行遍历
  // 此时Test_fn构造函数实例化出来的对象的属性都变成Use_apply构造函数实例化出来的对象的属性

  this.sex = sex;
}

var test = new Use_apply('yy', '17', 1); // 构造函数实例化
console.log(test); // {name: "yy", age: "17", sex: 1}

```

**call示例**
```
function Test_fn(name, age) {
  this.name = name;
  this.age = age;
}

function Use_call(name, age, sex) {
  var _this = this;

  Test_fn.call(_this, name, age); // 方法的区别，传递的参数需要一一写入

  this.sex = sex;
}

var test = new Use_call('mm', '16', 0); // 构造函数实例化
console.log(test); // {name: "mm", age: "16", sex: 0}

```

**两者参数一的使用场景**

只为了获取arguments中的值时，可传入任意this指向作为第一个参数  
eg: Array.prototype.shift.apply([], arguments); // 截取arguments中的第一个值  
