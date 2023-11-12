```JavaScript
const formatDate = (date, format) => {
	const addZero = (data) => {
		if(data < 10) return '0' + data;
		return data;
	}

const obj = {
	'yyyy': date.getFullYear(),
	'yy': date.getFullYear() % 100,
	'MM': addZero(date.getMonth() + 1),
	'M': date.getMonth() + 1,
	'dd': addZero(date.getDate()),
	'd': date.getDate(),
	'HH': addZero(date.getHours()),
	'H': date.getHours(),
	'hh': addZero(date.getHours() % 12),
	'h': date.getHours() % 12,
	'mm': addZero(date.getMinutes()),
	'm': date.getMinutes(),
	'ss': addZero(date.getSeconds()),
	's': date.getSeconds(),
	'w': () {
		const arr = ['日', '一', '二', '三', '四', '五', '六'];
		return arr[date.getDay()];
		}
	}

	for(let i in obj) {
		format = format.replace(i, obj[i]);
	}

	return format;
}
```

- `Date` 对象的 `getMonth()` 方法返回的月份值是从 0 开始的

- 循环确实会遍历 `obj` 中的每一个键，但 `format.replace(i, obj[i])` 只有当 `format` 字符串实际上包含该键时才会执行替换操作。如果`format` 字符串不包含某个键，那么 `replace` 方法将不执行任何操作，返回原始字符串，而不做任何更改

##### String.prototype.replace()

[String.prototype.replace() - JavaScript | MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/replace)
