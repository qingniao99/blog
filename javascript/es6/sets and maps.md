# sets and maps
Set对象是值的集合，你可以按照插入的顺序迭代它的元素。 Set中的元素只会出现一次，即 Set 中的元素是唯一的。
Map对象保存键值对。任何值(对象或者原始值) 都可以作为一个键或一个值。

在es5里面，set和map通常都是用对象来模拟的，你看:

```javascript
let set = Object.create(null);

set.foo = true;

// checking for existence
if (set.foo) {

    // do something
}
```

```javascript
let map = Object.create(null);

map.foo = "bar";

// retrieving a value
let value = map.foo;

console.log(value);         // "bar"
```

在进行模拟使用时，弊端很明显，因为传统的对象机制，key是string,这会使得你传入的key,都被字符串化：

```javascript
let map = Object.create(null);

map[5] = "foo";

console.log(map["5"]);      // "foo"


let map = Object.create(null),
    key1 = {},
    key2 = {};

map[key1] = "foo";

console.log(map[key2]);     // "foo"
```

在判断某个值得时候，会发生之判断key，不判断value得情况:

```javascript
let map = Object.create(null);

map.count = 1;

// checking for the existence of "count" or a nonzero value?
if (map.count) {
    // ...
}
```

## es6大法好

### Set

es6里面Set是一个顺序列表，其中的值没有重复的。

```javascript
let set = new Set();
set.add(5);
set.add("5");

console.log(set.size);    // 2
```

The only exception is that -0 and +0 are considered to be the same 
同时，即使是两个对象,也是可以的

```javascript
let set = new Set(),
    key1 = {},
    key2 = {};

set.add(key1);
set.add(key2);

console.log(set.size);    // 2
```

如果add方法接受的参数是同一个值，那后面的就会被ignore

```javascript
let set = new Set();
set.add(5);
set.add("5");
set.add(5);     // duplicate - this is ignored

console.log(set.size);    // 2
```
可以用数组初始化一个Set,Set会确保value的唯一性

```javascript
let set = new Set([1, 2, 3, 4, 5, 5, 5, 5]);
console.log(set.size);    // 5
```

```javascript
let set = new Set();
set.add(5);
set.add("5");

console.log(set.has(5));    // true
console.log(set.has(6));    // false
```

想要删除Set可以使用delete方法，清空用clear:

```javascript
let set = new Set();
set.add(5);
set.add("5");

console.log(set.has(5));    // true

set.delete(5);

console.log(set.has(5));    // false
console.log(set.size);      // 1

set.clear();

console.log(set.has("5"));  // false
console.log(set.size);      // 0
```

Set提供了forEach方法来遍历自己，用法和array还有map上的一样，唯一的不同点是，接收函数的第二个参数因为没有所以index,所以第二个参数的值和第一个是一样的：

```javascript
let set = new Set([1, 2]);

set.forEach(function(value, key, ownerSet) {
    console.log(key + " " + value);
    console.log(ownerSet === set);
});

1 1
true
2 2
true
```

Set的forEach第二个参数，类似于func.bind的使用，可以传递一个作用域进去，让第一个接受函数的内部this指向传递进去的环境,当然箭头函数也可实现

```javascript
let set = new Set([1, 2]);

let processor = {
    output(value) {
        console.log(value);
    },
    process(dataSet) {
        dataSet.forEach(function(value) {
            this.output(value);
        }, this);
    }
};

processor.process(set);


let set = new Set([1, 2]);

let processor = {
    output(value) {
        console.log(value);
    },
    process(dataSet) {
        dataSet.forEach((value) => this.output(value));
    }
};

processor.process(set);
```

有了Set,可以实现快捷的数组去重

```javascript
let set = new Set([1, 2, 3, 3, 3, 4, 5]),
    array = [...set];

console.log(array);             // [1,2,3,4,5]


function eliminateDuplicates(items) {
    return [...new Set(items)];
}

let numbers = [1, 2, 3, 3, 3, 4, 5],
    noDuplicates = eliminateDuplicates(numbers);

console.log(noDuplicates);      // [1,2,3,4,5]
```

### Weak Sets

```javascript
let set = new Set(),
    key = {};

set.add(key);
console.log(set.size);      // 1

// eliminate original reference
key = null;

console.log(set.size);      // 1

// get the original reference back
key = [...set][0];
```

如上所示，垃圾回收机制失效了，为了解决这个问题，weak set来了

```javascript
let set = new WeakSet(),
    key = {};

// add the object to the set
set.add(key);

console.log(set.has(key));      // true

set.delete(key);

console.log(set.has(key));      // false

let key1 = {},
    key2 = {},
    set = new WeakSet([key1, key2]);

console.log(set.has(key1));     // true
console.log(set.has(key2));     // true

//but

let set = new WeakSet(),
    key = {};

// add the object to the set
set.add(key);

console.log(set.has(key));      // true

// remove the last strong reference to key, also removes from weak set
key = null;
```

1 In a WeakSet instance, the add() method throws an error when passed a non-object (has() and delete() always return false for non-object arguments).
2 Weak sets are not iterables and therefore cannot be used in a for-of loop.
3 Weak sets do not expose any iterators (such as the keys() and values() methods), so there is no way to programmatically determine the contents of a weak set.
4 Weak sets do not have a forEach() method.
5 Weak sets do not have a size property.

如果只想用对象来作为key进行set化，那weak set无疑很好用

## Map

es6中的Map也是一个序列值的集合，它的key和value可以是任何类型的。

```javascript
let map = new Map();
map.set("title", "Understanding ES6");
map.set("year", 2016);

console.log(map.get("title"));      // "Understanding ES6"
console.log(map.get("year"));       // 2016

let map = new Map(),
    key1 = {},
    key2 = {};

map.set(key1, 5);
map.set(key2, 42);

console.log(map.get(key1));         // 5
console.log(map.get(key2));         // 42
```

Map的方法：
has(key) - Determines if the given key exists in the map
delete(key) - Removes the key and its associated value from the map
clear() - Removes all keys and values from the map

```javascript
let map = new Map();
map.set("name", "Nicholas");
map.set("age", 25);

console.log(map.size);          // 2

console.log(map.has("name"));   // true
console.log(map.get("name"));   // "Nicholas"

console.log(map.has("age"));    // true
console.log(map.get("age"));    // 25

map.delete("name");
console.log(map.has("name"));   // false
console.log(map.get("name"));   // undefined
console.log(map.size);          // 1

map.clear();
console.log(map.has("name"));   // false
console.log(map.get("name"));   // undefined
console.log(map.has("age"));    // false
console.log(map.get("age"));    // undefined
console.log(map.size);          // 0
```

Map的初始化通常是使用一个二维数组

```javascript
let map = new Map([["name", "Nicholas"], ["age", 25]]);

console.log(map.has("name"));   // true
console.log(map.get("name"));   // "Nicholas"
console.log(map.has("age"));    // true
console.log(map.get("age"));    // 25
console.log(map.size);          // 2
```

Map的forEach

```javascript
let map = new Map([ ["name", "Nicholas"], ["age", 25]]);

map.forEach(function(value, key, ownerMap) {
    console.log(key + " " + value);
    console.log(ownerMap === map);
});

name Nicholas
true
age 25
true
```

同样，Map的forEach第二个参数也可以传递一个指定作用域进去

### weak map

同set一样，为了解决垃圾回首机制的弊端，产生了weak map

```javascript
let map = new WeakMap(),
    element = document.querySelector(".element");

map.set(element, "Original");

let value = map.get(element);
console.log(value);             // "Original"

// remove the element
element.parentNode.removeChild(element);
element = null;

// the weak map is empty at this point
```

同weak set一样，weak map的key也只能是object,方法也只有has delete(require enumerating keys, and like weak sets, that isn't possible with weak maps. )

```javascript
let key1 = {},
    key2 = {},
    map = new WeakMap([[key1, "Hello"], [key2, 42]]);

console.log(map.has(key1));     // true
console.log(map.get(key1));     // "Hello"
console.log(map.has(key2));     // true
console.log(map.get(key2));     // 42

let map = new WeakMap(),
    element = document.querySelector(".element");

map.set(element, "Original");

console.log(map.has(element));   // true
console.log(map.get(element));   // "Original"

map.delete(element);
console.log(map.has(element));   // false
console.log(map.get(element));   // undefined
```
weak map的一个妙处是可以用来模拟私有变量

```javascript
function Person(name) {
    this._name = name;
}

Person.prototype.getName = function() {
    return this._name;
};


var Person = (function() {

    var privateData = {},
        privateId = 0;

    function Person(name) {
        Object.defineProperty(this, "_id", { value: privateId++ });

        privateData[this._id] = {
            name: name
        };
    }

    Person.prototype.getName = function() {
        return privateData[this._id].name;
    };

    return Person;
}());


let Person = (function() {

    let privateData = new WeakMap();

    function Person(name) {
        privateData.set(this, { name: name });
    }

    Person.prototype.getName = function() {
        return privateData.get(this).name;
    };

    return Person;
}());
```

## summary

ECMAScript 6 formally introduces sets and maps into JavaScript. Prior to this, developers frequently used objects to mimic both sets and maps, often running into problems due to the limitations associated with object properties.

Sets are ordered lists of unique values. Values are not coerced to determine equivalence. Sets automatically remove duplicate values, so you can use a set to filter an array for duplicates and return the result. Sets aren't subclasses of arrays, so you cannot randomly access a set's values. Instead, you can use the has() method to determine if a value is contained in the set and the size property to inspect the number of values in the set. The Set type also has a forEach() method to process each set value.

Weak sets are special sets that can contain only objects. The objects are stored with weak references, meaning that an item in a weak set will not block garbage collection if that item is the only remaining reference to an object. Weak set contents can't be inspected due to the complexities of memory management, so it's best to use weak sets only for tracking objects that need to be grouped together.

Maps are ordered key-value pairs where the key can be any data type. Similar to sets, keys are not coerced to determine equivalence, which means you can have a numeric key 5 and a string "5" as two separate keys. A value of any data type can be associated with a key using the set() method, and that value can later be retrieved by using the get() method. Maps also have a size property and a forEach() method to allow for easier item access.

Weak maps are a special type of map that can only have object keys. As with weak sets, an object key reference is weak and doesn't prevent garbage collection when it's the only remaining reference to an object. When a key is garbage collected, the value associated with the key is also removed from the weak map. This memory management aspect makes weak maps uniquely suited for correlating additional information with objects whose lifecycles are managed outside of the code accessing them.