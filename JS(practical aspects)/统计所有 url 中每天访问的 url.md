```js
class urlTracker {
  constructor() {
    this.visited = {};
  }
  record(url, date) {
    if (!this.visited[date]) this.visited[date] = new Set();
    this.visited[date].add(url);
  }
  getVisitedUrls(date) {
    return this.visited[date] ? Array.from(this.visited[date]) : [];
  }
}

const tracker = new urlTracker();
tracker.record("https://github.com/Rainyrou/FE-Clown", "2024-04-05");
tracker.record("https://github.com/Rainyrou", "2024-04-05");
tracker.record("https://github.com/Rainyrou/Pocket-React", "2024-04-06");

console.log(tracker.getVisitedUrls("2024-04-05")); // [ 'https://github.com/Rainyrou/FE-Clown', https://github.com/Rainyrou' ]
console.log(tracker.getVisitedUrls("2024-04-06")); // [ 'https://github.com/Rainyrou/Pocket-React' ]
```