## 浅度克隆与深度克隆

浅度克隆：原始类型为值传递，对象类型仍为引用传递。  
深度克隆：所有元素或属性均完全复制，与原对象完全脱离，也就是说所有对于新对象的修改都不会反映到原对象中。

**深度克隆**

判断类型的正确方式：`Object.prototype.toString.call().slice(8, -1)`

为什么不直接用 toString 方法？  
这是为了防止对象中的 toString 方法被重写，为了正确的调用 toString()版本，必须间接的调用 Function.call()方法

为什么不使用 typeof 来直接判断类型？  
因为对于 Array 而言，使用 typeof（Array）返回的是 object，所以不能得到正确的 Array，这里对于后续的数组克隆将产生致命的问题

```javascript
// 深度克隆
function deepClone(obj) {
  var result = null,
    oClass = isClass(obj);
  // 判断克隆目标的类型 一般分三种类型 Object Array 其他
  if (oClass === 'Object') {
    result = {};
  } else if (oClass === 'Array') {
    result = [];
  } else {
    return obj;
  }
  // for...in obj
  for (var k in obj) {
    var copy = obj[k]; // 克隆目标
    if (isClass(copy) === 'Object') {
      result[k] = arguments.callee(copy); // 递归调用
    } else if (isClass(copy) === 'Array') {
      result[k] = arguments.callee(copy);
    } else {
      result[k] = copy; // 简单类型
    }
  }
  return result;
}
// 判断类型
function isClass(target) {
  return Object.prototype.toString.call(target).slice(8, -1);
}

var obj = {
  id: '1',
  count: 2,
  dataObj: {
    o: 'ooo'
  },
  dataArr: [
    123,
    {
      a: [1, 2, 3]
    }
  ]
};

// 使用
var newObj = deepClone(obj); // 克隆obj
newObj.id = '2'; // 对newObj的属性重新赋值

// 比较本体与克隆体
console.log(newObj.id); // 2
console.log(obj.id); // 1
```
