```js
const maxGap = (nums) => {
  if (nums.length < 2) return 0;
  let min = Math.min(...nums),
    max = Math.max(...nums);
  if (min === max) return 0;
  const len = nums.length;
  const bucketSize = Math.ceil((max - min) / (len - 1));
  const bucketCount = Math.floor((max - min) / bucketSize) + 1;
  const buckets = new Array(bucketCount)
    .fill(null)
    .map(() => ({ min: Infinity, max: -Infinity, used: false }));
  for (const num of nums) {
    const idx = Math.floor((num - min) / bucketSize);
    buckets[idx].used = true;
    buckets[idx].min = Math.min(buckets[idx].min, num);
    buckets[idx].max = Math.max(buckets[idx].max, num);
  }
  let maxGap = 0,
    preMax = min;
  for (const bucket of buckets) {
    if (!bucket.used) continue;
    maxGap = Math.max(maxGap, bucket.min - preMax);
    preMax = bucket.max;
  }
  return maxGap;
};

const arr = [3, 6, 9, 1];
console.log(maxGap(arr)); // 3
```

