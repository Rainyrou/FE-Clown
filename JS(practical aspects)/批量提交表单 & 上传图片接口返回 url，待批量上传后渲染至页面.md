- 合并多表单数据，一次请求提交
- 多请求并发提交

```js
const getFormData = (formElement) => {
  const formData = {};
  const inputs = formElement.querySelector("[name]");
  inputs.forEach((input) => {
    const { name, type, value, checked } = input;
    if (type === "checkbox") {
      if (!formData[name]) formData[name] = [];
      if (checked) formData[name].push(value);
    } else if (type === "radio" && checked) {
      formData[name] = value;
    } else {
      formData[name] = value;
    }
  });
  return formData;
};

const getAllFormData = (formSelector) => {
  const allForms = document.querySelectorAll(formSelector);
  const batchData = [];
  allForms.forEach((form) => {
    if (!validateForm) {
      console.warn(`${form} fail`);
      return;
    }
    batchData.push(getFormData(form));
  });
  return batchData;
};

const validateForm = (form) => {
  const requiredInputs = form.querySelectorAll("[required]");
  for (const input of requiredInputs) {
    if (!input.trim()) {
      alert(`${input.placeholder}`);
      return false;
    }
  }
  return true;
};

const batchSubmitForms = async (formSelector, url) => {
  const batchData = getAllFormData(formSelector);
  if (batchData.length === 0) return;
  try {
    const response = await fetch(url, {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify(batchData),
    });
    const result = await response.json();
    if (response.ok) {
      alert("sumbit success");
      document.querySelectorAll(formSelector).forEach((form) => form.reset());
    } else {
      alert(result.message);
    }
  } catch (err) {
    console.error(err);
  }
};

document
  .getElementById("submit")
  .addEventListener("click", () =>
    batchSubmitForms(".batch-form", "/api/batch-save")
  );
```

```js
const uploadImage = (file) =>
  new Promise((resolve, reject) =>
    setTimeout(
      () => resolve(`https://cdn.example.com/${file.name}_${Date.now()}.webp`),
      Math.random() * 2000 + 1000
    )
  );

const batchFetch = async (fileList) => {
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
