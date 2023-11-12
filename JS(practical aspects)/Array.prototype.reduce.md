```JavaScript
Array.prototype._myreduce = function(callback, initialVal) {
    if(typeof callback !== 'function') {
        throw new TypeError(`${callback} is not a function`);
    }
    if(this == null || typeof this[Symbol.iterator] !== 'function') {
        throw new TypeError(`${this} is not a iterator`);
    }
    const arr = [...this];
    let accumulator;
    if(initialVal !== undefined) {
        accumulator = initialVal;
    } else {    
        if(arr.length === 0) {
            throw new TypeError('Reduce of empty array with no initial value');
        }
        accumulator = arr.shift();
    }
    for(let i = 0;i < arr.length;++i) {
        accumulator = callback(accumulator, arr[i], i, this);
    }
    return accumulator;
};
```

测试用例：

```JavaScript
const numbers = [1, 2, 3, 4, 5];
const sum = numbers._myreduce((acc, num) => acc + num, 0);
console.log(sum);  // 15

const product = numbers._myreduce((acc, num) => acc * num, 1);
console.log(product);  // 120
```


