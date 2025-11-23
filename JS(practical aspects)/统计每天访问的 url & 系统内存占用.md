统计每天访问的 url：

```js
class Tracker {
  constructor() {
    this.visited = {};
  }
  set(url, date) {
    if (!this.visited[date]) this.visited[date] = [];
    this.visited[date].push(url);
  }
  get(date) {
    return this.visited[date];
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
const findMaxMemoryUsage = (requests) => {
  const events = [];
  requests.forEach(({ id, memory, start, end }) => {
    events.push({ time: start, type: "start", memory, id });
    events.push({ time: end, type: "end", memory, id });
  });
  // 时间相同时，结束事件先于开始事件以释放内存后再分配
  events.sort((a, b) => a.time - b.time || (a.type === "end" ? -1 : 1));
  let maxMemory = 0,
    curMemory = 0,
    maxIds = [];
  const curIds = new Set();
  for (const event of events) {
    if (event.type === "start") {
      curMemory += event.memory;
      curIds.add(event.id);
    } else {
      curMemory -= event.memory;
      curIds.delete(event.id);
    }
    if (curMemory > maxMemory) {
      maxMemory = curMemory;
      maxIds = [...curIds];
    }
  }
  return { maxMemory, maxIds };
};

const requests = [
  { id: 1, memory: 100, start: 1, end: 5 },
  { id: 2, memory: 200, start: 2, end: 6 },
  { id: 3, memory: 150, start: 4, end: 8 },
];

const result = findMaxMemoryUsage(requests);
console.log(result);
```