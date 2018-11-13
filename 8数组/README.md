# 数组的扩展



# 一、扩展运算符(...)



## 1、数组复制（不是新数组）

```javascript
const a1 = [4, 5, 6, 7]
const a2 = a1
a2[0] = 666
console.log('a1', a1)  //[666, 5, 6, 7]
```

> 上面的示例，只是复制了指向底层数据结构的指针，而不是克隆一个全新的数组。
>
> `a2`并不是`a1`的克隆，而是指向同一份数据的另一个指针。修改`a2`，会直接导致`a1`的变化。



## 2、克隆新数组

- ES5的写法（只能用变通方法来复制数组）

```javascript
const a1 = ['a', 'b', 'c']
const a2 = a1.concat()
a2[0] = 'hello'
console.log('a1=', a1)  //  ["a", "b", "c"]
```

> `a1`会返回原数组的克隆，再修改`a2`就不会对`a1`产生影响。



- 扩展运算符

```javascript
const a1 = [4, 3, 2, 1]
const a2 = [...a1]
a2[0] = 666
console.log('a1', a1)  //[4, 3, 2, 1]
```
或者


```javascript
const b1 = ['q', 'w', 'e']
const [...b2] = b1
b2[0] = 'r'
console.log('b1', b1)  // ["q", "w", "e"]
```



## 3、数组合并

- ES5的写法

```javascript
const a1 = [1, 2, 3]
const a2 = ['a', 'b']
const a3 = [666, 777, 888]
const a4 =  a1.concat(a2, a3)
console.log('a4', a4) // [1, 2, 3, "a", "b", 666, 777, 888]
```

- ES6写法

```javascript
const a1 = [1, 2, 3]
const a2 = ['a', 'b']
const a3 = [666, 777, 888]
const a5 = [...a1, ...a2, ...a3]
console.log('a5', a5) // [1, 2, 3, "a", "b", 666, 777, 888]
```

> 上面2种方法都是浅拷贝，使用要注意。

```javascript
let a1 = [{ foo: 1 }];
let a2 = [{ bar: 2 }];

const a3 = a1.concat(a2);
const a4 = [...a1, ...a2];

console.log(a3[0] === a1[0]) // true
console.log(a4[0] === a1[0]) // true

a1[0].foo = 666 // 修改原数组的成员

console.log('a3', JSON.stringify(a3))  // [{"foo":666},{"bar":2}]
console.log('a4', JSON.stringify(a4))  // [{"foo":666},{"bar":2}]

console.log(a3[0] === a1[0]) // false
console.log(a4[0] === a1[0]) // false
```

> 上面代码中，`a3`和`a4`是用两种不同方法合并而成的新数组，但是它们的成员都是对原数组成员的引用，这就是浅拷贝。如果修改了原数组的成员，会同步反映到新数组。





# 二、Array.from()

> `Array.from`方法用于将两类对象转为真正的数组：类似数组的对象（array-like object）和可遍历（iterable）的对象（包括 ES6 新增的数据结构 Set 和 Map）

## 1、转真正数组

```javascript
let arrayLike = {
    0: 'a',
    1: 'b',
    2: 'c',
    length: 3
}

// ES5写法
let arr1 = [].slice.call(arrayLike)
console.log('arr1', JSON.stringify(arr1)) // ["a","b","c"]

// ES6写法
let arr2 = Array.from(arrayLike)
console.log('arr2', JSON.stringify(arr2))  // ["a","b","c"]
```



## 2、克隆数据

> 如果参数是一个真正的数组，`Array.from`会返回一个一模一样的新数组。

```javascript
let arr1 = [1, 3, 5]
let arr2 = Array.from(arr1)

arr1[1] = 10

console.log('arr1', JSON.stringify(arr1)) // [1,10,5]
console.log('arr2', JSON.stringify(arr2)) // [1,3,5]
```



3、对每个元素进行处理

```javascript
let arr1 = [1, 3, 5]
let arr2 = Array.from(arr1, x => x * 2)               // 写法1
let arr3 = Array.from(arr1).map( f => f * 3)		  // 写法2
console.log('arr2', arr2) //  [2, 6, 10]
console.log('arr3', arr3)
```



# 三、Array.of()

> `Array.of`方法用于将一组值，转换为数组。

```javascript
let arr1 = Array.of('a', 1, 'b', 2, 'c')
console.log(arr1)  // ["a", 1, "b", 2, "c"]
```

> `Array.of`基本上可以用来替代`Array()`或`new Array()`，并且不存在由于参数不同而导致的重载。它的行为非常统一。

```javascript
Array.of() // []
Array.of(undefined) // [undefined]
Array.of(1) // [1]
Array.of(1, 2) // [1, 2]
```

> `Array.of`总是返回参数值组成的数组。如果没有参数，就返回一个空数组。



# 四、copyWithin()

> 在当前数组内部，将指定位置的成员复制到其他位置（会覆盖原有成员），然后返回当前数组。也就是说，使用这个方法，会修改当前数组。

```javascript
Array.prototype.copyWithin(target, start = 0, end = this.length)
```

它接受三个参数。

- target（必需）：从该位置开始替换数据。如果为负值，表示倒数。
- start（可选）：从该位置开始读取数据，默认为 0。如果为负值，表示倒数。
- end（可选）：到该位置前停止读取数据，默认等于数组长度。如果为负值，表示倒数



# 五、find()和findIndex()

## find()

> 数组实例的`find`方法，用于找出**第一个**符合条件的数组成员。它的参数是一个回调函数，所有数组成员依次执行该回调函数，直到找出第一个返回值为`true`的成员，然后返回该成员。如果没有符合条件的成员，则返回`undefined`。

```javascript
const a1 = [1, 2, 3, 4, 10, 13].find(item => item > 9)
console.log('a1', a1) //10
```

```javascript
const a2 = [1, 2, 3, 4, 10, 13].find((value, index, arr) => {
    return value > 9
})
```

> `find`方法的回调函数可以接受三个参数，依次为当前的值、当前的位置和原数组。



## findIndex()

> 数组实例的`findIndex`方法的用法与`find`方法非常类似，返回第一个符合条件的数组成员的位置，如果所有成员都不符合条件，则返回`-1`。

```javascript
[1, 5, 10, 15].findIndex(function(value, index, arr) {
  return value > 9;
}) // 2
```

> 上面的2个方法，都可以发现`NaN`，弥补了数组的`indexOf`方法的不足。

```javascript
[NaN].indexOf(NaN)
// -1

[NaN].findIndex(y => Object.is(NaN, y))
// 0
```

> 上面代码中，`indexOf`方法无法识别数组的`NaN`成员，但是`findIndex`方法可以借助`Object.is`方法做到。



# 六、fill()

> `fill`方法使用给定值，填充一个数组。

```javascript
['a', 'b', 'c'].fill(7)
// [7, 7, 7]

new Array(3).fill(7)
// [7, 7, 7]
```

> `fill`方法用于空数组的初始化非常方便。数组中已有的元素，会被全部抹去。

> 注意，如果填充的类型为对象，那么被赋值的是同一个内存地址的对象，而不是深拷贝对象。

```javascript
let arr = new Array(3).fill({name: "Mike"});
arr[0].name = "Ben";
arr
// [{name: "Ben"}, {name: "Ben"}, {name: "Ben"}]

let arr = new Array(3).fill([]);
arr[0].push(5);
arr
// [[5], [5], [5]]
```



# 七、entries()，keys()和values()

> ES6 提供三个新的方法——`entries()`，`keys()`和`values()`——用于遍历数组。
>
> 它们都返回一个遍历器对象`Iterator`，可以用`for...of`循环进行遍历，唯一的区别是：
>
> `keys()`是对键名的遍历、
>
> `values()`是对键值的遍历，
>
> `entries()`是对键值对的遍历



```javascript
for (let index of ['a', 'b', 'c'].keys()) {
    console.log('index=', index)  // 0 1 2
}


for (let elem of ['a', 'b', 'c'].values()) {
    console.log('value=', elem)  // a b  c
}

for (let [index, elem] of ['a', 'b', 'c'].entries()) {
    console.log(index, elem)
}
// 0 "a" 
// 1 "b"
// 2 "c"
```



# 八、includes()

> `Array.prototype.includes`方法返回一个布尔值，表示某个数组是否包含给定的值，与字符串的`includes`方法类似

```javascript
console.log([1, 3, 5].includes(1)) // true
console.log([1, 3, 5].includes(999)) // false
console.log([1, 3, NaN].includes(NaN)) // true
```



# 九、flat() 和 flatMap()

## flat()

> 数组的成员有时还是数组，`Array.prototype.flat()`用于将嵌套的数组“拉平”，变成一维的数组。该方法返回一个新数组，对原数据没有影响

```javascript
console.log([1, 2, [3], 5].flat()) // [1, 2, 3, 5]
```

> `flat()`默认只会“拉平”一层，如果想要“拉平”多层的嵌套数组，可以将`flat()`方法的参数写成一个整数，表示想要拉平的层数，默认为1

```javascript
[1, 2, [3, [4, 5]]].flat()
// [1, 2, 3, [4, 5]]

[1, 2, [3, [4, 5]]].flat(2)
// [1, 2, 3, 4, 5]
```

> 如果不管有多少层嵌套，都要转成一维数组，可以用`Infinity`关键字作为参数。

```javascript
[1, [2, [3]]].flat(Infinity)
// [1, 2, 3]
```

## flatMap()

> `flatMap()`方法对原数组的每个成员执行一个函数（相当于执行`Array.prototype.map()`），然后对返回值组成的数组执行`flat()`方法。该方法返回一个新数组，不改变原数组。

```javascript
// 相当于 [[2, 4], [3, 6], [4, 8]].flat()
[2, 3, 4].flatMap((x) => [x, x * 2])
// [2, 4, 3, 6, 4, 8]
```



# 十、数组的空位

> 数组的空位指，数组的某一个位置没有任何值。
>
> 由于空位的处理规则非常不统一，所以建议避免出现空位。









----

# TODO LIST

- 一个数组是否包含另一个数组？