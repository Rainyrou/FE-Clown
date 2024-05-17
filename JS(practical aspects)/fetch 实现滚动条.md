```js
document.getElementById("downloadBtn").addEventListener("click", () => {
  const url = "https://example.com/large-file";
  const progressBar = document.getElementById("progress-bar");
  fetch(url)
    .then((response) => {
      if (!response.ok) throw new Error("Network response was not ok");
      const contentLength = response.headers.get("Content-Length");
      if (!contentLength)
        throw new Error("Content-Length response header unavailable");
      const total = parseInt(contentLength, 10);
      let loaded = 0;
      return new Response(
        new ReadableStream({
          start(controller) {
            const reader = response.body.getReader();
            function read() {
              reader
                .read()
                .then(({ done, value }) => {
                  if (done) {
                    controller.close();
                    return;
                  }
                  loaded += value.byteLength;
                  progressBar.style.width = `${(loaded / total) * 100}%`;
                  controller.enqueue(value);
                  read();
                })
                .catch((error) => {
                  console.error("Stream reading error:", error);
                  controller.error(error);
                });
            }
            read();
          },
        })
      );
    })
    .then((response) => response.blob())
    .then((blob) => {
      const url = URL.createObjectURL(blob);
      const a = document.createElement("a");
      a.href = url;
      a.download = "downloaded-file";
      document.body.appendChild(a);
      a.click();
      a.remove();
      URL.revokeObjectURL(url);
    })
    .catch((error) => console.error("Fetch error:", error));
});
```