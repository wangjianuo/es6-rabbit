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

## 1、转真正数据

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

