非空 `<table>` 至少包含一个 `<tr>`，且该 `<tr>` 至少包含一个 `<td>` 或 `<th>`，该 `<td>` 或 `<th>` 包含非空白内容

```js
const getNoEmptyTables = () => {
  const tables = document.querySelectorAll("table");
  const noEmptyTables = Array.from(tables).filter((table) => {
    const trs = table.querySelectorAll("tr");
    return Array.from(trs).some((tr) => {
      const cells = tr.querySelectorAll("td, th");
      return Array.from(cells).some((cell) => cell.textContent.trim() !== "");
    });
  });
  return noEmptyTables;
};

const noEmptyTables = getNoEmptyTables();
console.log(noEmptyTables);
```

![[1712416976748.png]]