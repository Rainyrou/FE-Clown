```js
Array.prototype.findDuplicate = function (n) {
  const map = this.reduce((acc, item) => {
    acc[item] = (acc[item] || 0) + 1;
    return acc;
  }, {});
  return Object.keys(map).filter((key) => map[key] >= n);
};

const arr1 = [1, 2, 3, 1, 2, 1];
console.log(arr1.findDuplicate(2)); // [ '1', '2' ]

const arr2 = ["apple", "banana", "apple", "apple", "banana", "cherry"];
console.log(arr2.findDuplicate(3)); // [ 'apple' ]

const arr3 = [true, false, true, true];
console.log(arr3.findDuplicate(2)); // [ 'true' ]
```