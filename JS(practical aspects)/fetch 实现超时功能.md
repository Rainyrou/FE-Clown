```js
function fetchWithTimeout(url, options = {}, timeout = 5000) {
  const { signal } = options;
  const controller = new AbortController();
  const timeoutId = setTimeout(() => controller.abort(), timeout);
  return fetch(url, {
    ...options,
    signal: signal || controller.signal,
  }).finally(() => clearTimeout(timeoutId));
}

fetchWithTimeout("https://jsonplaceholder.typicode.com/posts", {}, 3000)
  .then((response) => response.json())
  .then((data) => console.log(data))
  .catch((error) => {
    if (error.name === "AbortError") console.error("Fetch request timed out");
    else console.error("Fetch request failed", error);
  });
```

