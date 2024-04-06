```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>textarea</title>
</head>
<body>
    <textarea id="autoresizing" rows="3" style="overflow-y:hidden;"></textarea>
    <script>
        const textarea = document.getElementById('autoresizing');
        textarea.addEventListener('input', autoResize, false);
        function autoResize() {
            this.style.height = 'auto';
            this.style.height = this.scrollHeight + 'px';
        }
    </script>
</body>
</html>
```