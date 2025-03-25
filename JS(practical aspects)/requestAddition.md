```js
const requestAddition = (a, b) =>
  new Promise((resolve) => setTimeout(() => resolve(a + b), 1000));
const calculate = (...nums) => {
  if (nums.length === 0) return Promise.resolve(0);
  if (nums.length === 1) return Promise.resolve(nums[0]);
  let promiseChain = requestAddition(nums[0], nums[1]);
  for (let i = 2; i < nums.length; ++i)
    promiseChain = promiseChain.then((res) => requestAddition(res, nums[i]));
  return promiseChain;
};

calculate(1, 2, 3, 4)
  .then((res) => console.log(res))
  .catch((err) => console.error(err));
```