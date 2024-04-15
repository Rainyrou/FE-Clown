```HTML
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>v-model</title>
</head>
<body>
    <div>
        <button id="myBtn">change</button>
        <input type="text" id="myInput">
        <h1 id="myTitle"></h1>
    </div>
    <script>
        let userInfo = { username: 'clown' };

        const inputElement = document.querySelector('#myInput');
        const titleElement = document.querySelector('#myTitle');

        function watcher() {
            Object.defineProperty(userInfo, 'username', {
                set(value) {
                    inputElement.value = value;
                    titleElement.innerHTML = value;
                }
                get() {
                    return this.username;
                }
            })
        }

        watcher();
        userInfo.username = 'clown';

        inputElement.oninput = e => userInfo.username = e.target.value;
        document.querySelector('#myBtn').onclick = () => userInfo.username = 'clown';
    </script>
</body>
</html>
```