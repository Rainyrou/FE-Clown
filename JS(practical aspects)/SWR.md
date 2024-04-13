```js
class SWR {
  constructor() {
    this.cache = new Map();
  }
  async fetchWithSWR(key, fetcher) {
    if (this.cache.has(key)) {
      setTimeout(async () => {
        const data = await fetcher();
        this.cache.set(key, data);
        console.log(`Data for ${key} updated in the background.`);
      }, 0);
      return this.cache.get(key);
    } else {
      const data = await fetcher();
      this.cache.set(key, data);
      return data;
    }
  }
}

const swr = new SWR();
const fetcher = () =>
  fetch("https://jsonplaceholder.typicode.com/posts/1").then((res) =>
    res.json()
  );
async function loadData() {
  const data = await swr.fetchWithSWR("postData", fetcher);
  console.log(data);
}
loadData();
```

![[Pasted image 20240414013807.png]]