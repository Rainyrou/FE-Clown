```js
Promise._all = (promises) => {
  return new Promise((resolve, reject) => {
    if (promises == null || typeof promises[Symbol.iterator] !== "function")
      throw new TypeError(`${promises} is not an iterator`);
    promises = [...promises];
    if (promises.length === 0) return resolve([]);
    let count = 0;
    const values = [];
    promises.forEach((promise, index) =>
      Promise.resolve(promise)
        .then((res) => {
          values[index] = res;
          if (++count === promises.length) return resolve(values);
        })
        .catch(reject)
    );
  });
};

function test() {
  try {
    Promise._all(null).then(
      (res) => console.log(res),
      (rej) => console.log(rej)
    );
  } catch (e) {
    console.log(e); // TypeError: null is not a iterator
  }

  try {
    Promise._all({}).then(
      (res) => console.log(res),
      (rej) => console.log(rej)
    );
  } catch (e) {
    console.log(e); // TypeError: [object Object] is not a iterator
  }

  Promise._all([]).then(
    (res) => console.log(res),
    (rej) => console.log(rej)
  ); // []
  
  Promise._all(new Set()).then(
    (res) => console.log(res),
    (rej) => console.log(rej)
  ); // []

  Promise._all(new Map()).then(
    (res) => console.log(res),
    (rej) => console.log(rej)
  ); // []

  Promise._all([
    Promise.resolve(1),
    Promise.resolve(2),
    Promise.resolve(3),
    4,
  ]).then(
    (res) => console.log(res),
    (rej) => console.log(rej)
  ); // [ 1, 2, 3, 4 ]

  Promise._all([
    Promise.reject(1),
    Promise.resolve(2),
    Promise.resolve(3),
    4,
  ]).then(
    (res) => console.log(res),
    (rej) => console.log(rej)
  ); // 1
}

test();

