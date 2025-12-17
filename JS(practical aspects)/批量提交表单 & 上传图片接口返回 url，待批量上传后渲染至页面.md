- 合并多表单数据，一次请求提交
- 多请求并发提交

```js
const batchSubmit = (url, dataList) => {
  return Promise.all(
    dataList.map((item) =>
      fetch(url, {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(item),
      }).then((res) => res.json())
    )
  );
};

const formsData = [{ id: 1 }, { id: 2 }, { id: 3 }];
batchSubmit("/api/save", formsData).then((results) => console.log(results));
```

上传图片接口返回 url，待批量上传后渲染至页面：

```js
const uploadImage = (file) =>
  new Promise((resolve, reject) =>
    setTimeout(
      () => resolve(`https://cdn.example.com/${file.name}_${Date.now()}.webp`),
      Math.random() * 2000 + 1000
    )
  );

const batchUpload = async (fileList) => {
  if (fileList.length === 0) return;
  const uploadPromises = Array.from(fileList).map((file) => uploadImage(file));
  try {
    const urls = await Promise.all(uploadPromises);
    urls.forEach((url) => {
      const img = document.createElement("img");
      img.src = url;
      img.style.maxWidth = "100px";
      img.style.maxHeight = "100px";
      document.body.appendChild(img);
    });
  } catch (error) {
    throw new Error(error);
  }
};
```
