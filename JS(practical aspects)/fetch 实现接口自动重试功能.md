```js
const fetchWithRetry = async (url, options = {}, retries = 3, delay = 1000) => {
  try {
    const response = await fetch(url, options);
    if (!response.ok)
      throw new Error(`Request failed with status ${response.status}`);
    return response;
  } catch (error) {
    if (retries) {
      console.warn(`Retrying...`);
      await new Promise((resolve) => setTimeout(resolve, delay));
      return fetchWithRetry(url, options, retries - 1, delay);
    } else {
      throw error;
    }
  }
};

const fetchTest = async () => {
  try {
    const response = await fetchWithRetry(
      "https://jsonplaceholder.typicode.com/posts/1",
      {},
      3,
      2000
    );
    const data = await response.json();
    console.log("Data: ", data);
  } catch (error) {
    console.error("Fetch failed: ", error);
  }
};

fetchTest();
```

