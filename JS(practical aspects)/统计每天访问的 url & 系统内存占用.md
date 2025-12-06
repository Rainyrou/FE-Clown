统计每天访问的 url：

```js
class Tracker {
  constructor() {
    this.map = {};
  }
  set(url, date) {
    if (!this.map[date]) this.map[date] = [];
    this.map[date].push(url);
  }
  get(date) {
    return this.map[date];
  }
}

const tracker = new Tracker();
tracker.set("https://github.com/Rainyrou/FE-Clown", "2024-04-05");
tracker.set("https://github.com/Rainyrou", "2024-04-05");
tracker.set("https://github.com/Rainyrou/Pocket-React", "2024-04-06");

console.log(tracker.get("2024-04-05")); // [ 'https://github.com/Rainyrou/FE-Clown', https://github.com/Rainyrou' ]
console.log(tracker.get("2024-04-06")); // [ 'https://github.com/Rainyrou/Pocket-React' ]
```

查找系统内存占用最大时对应的内存和事件：

```js
const find = (requests) => {
  const events = [];
  requests.forEach(({ id, memo, start, end }) => {
    events.push({ time: start, type: "start", memo, id });
    events.push({ time: end, type: "end", memo, id });
  });
  events.sort((a, b) => a.time - b.time || (a.type === "end" ? -1 : 1));
  let maxMemo = 0,
    curMemo = 0,
    maxIds = [];
  const curIds = new Set();
  for (const event of events) {
    if (event.type === "start") {
      curMemo += event.memo;
      curIds.add(event.id);
    } else {
      curMemo -= event.memo;
      curIds.delete(event.id);
    }
    if (curMemo > maxMemo) {
      maxMemo = curMemo;
      maxIds = [...curIds];
    }
  }
  return { maxMemo, maxIds };
};

const requests = [
  { id: 1, memo: 100, start: 1, end: 5 },
  { id: 2, memo: 200, start: 2, end: 6 },
  { id: 3, memo: 150, start: 4, end: 8 },
];

console.log(find(requests)); // { maxMemory: 450, maxIds: [ 1, 2, 3 ] }
```