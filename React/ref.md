字符串形式

```HTML
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hello React</title>
</head>
<body>
    <div id="test"></div>
    <script type="text/javascript" src="https://unpkg.com/react@17/umd/react.development.js"></script>
    <script type="text/javascript" src="https://unpkg.com/react-dom@17/umd/react-dom.development.js"></script>
    <script type="text/javascript" src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
    <script type="text/javascript" src="https://unpkg.com/prop-types@15.8.1/prop-types.js"></script>
    <script type="text/babel">
        class Demo extends React.Component {
            showData = () => {
                const { input1 } = this.refs;
                alert(input1.value);
            }

            showData2 = () => {
                const { input2 } = this.refs;
                alert(input2.value);
            }

            render() {
                return (
                    <div>
                        <input ref="input1" type="" placeholder="click button" />&nbsp;
                        <button onClick={this.showData}>click left</button>&nbsp;
                        <input ref="input2" onBlur={this.showData2} type="" placeholder="lose focus" />&nbsp;
                        <button>click right</button>
                    </div>
                )
            }
        }

        ReactDOM.render(<Demo />, document.getElementById('test'))
    </script>
</body>
</html>
```


回调形式

```HTML
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hello React</title>
</head>
<body>
    <div id="test"></div>
    <script type="text/javascript" src="https://unpkg.com/react@17/umd/react.development.js"></script>
    <script type="text/javascript" src="https://unpkg.com/react-dom@17/umd/react-dom.development.js"></script>
    <script type="text/javascript" src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
    <script type="text/javascript" src="https://unpkg.com/prop-types@15.8.1/prop-types.js"></script>
    <script type="text/babel">
        class Demo extends React.Component {
            showData = () => {
                const { input1 } = this;
                alert(input1.value);
            }

            showData2 = () => {
                const { input2 } = this;
                alert(input2.value);
            }

            render() {
                return (
                    <div>
                        <input ref={currentNode => this.input1 = currentNode} type="" placeholder="click button" />&nbsp;
                        <button onClick={this.showData}>click left</button>&nbsp;
                        <input ref={currentNode => this.input2 = currentNode} onBlur={this.showData2} type="" placeholder="lose focus" />&nbsp;
                        <button>click right</button>
                    </div>
                )
            }
        }

        ReactDOM.render(<Demo />, document.getElementById('test'))
    </script>
</body>
</html>
```

```HTML
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hello React</title>
</head>
<body>
    <div id="test"></div>
    <script type="text/javascript" src="https://unpkg.com/react@17/umd/react.development.js"></script>
    <script type="text/javascript" src="https://unpkg.com/react-dom@17/umd/react-dom.development.js"></script>
    <script type="text/javascript" src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
    <script type="text/javascript" src="https://unpkg.com/prop-types@15.8.1/prop-types.js"></script>
    <script type="text/babel">
        class Demo extends React.Component {
            state = { isHot: true };

            showInfo = () => {
                const { isHot } = this.state;
                this.setState({ isHot: !isHot })
                const { input1 } = this;
                alert(input1.value);
            }

            render() {
                const { isHot } = this.state;

                return (
                    <div>
                        <h2>{isHot ? 'hot' : 'cold'}</h2>
                        <input ref={(currentNode) => {this.input1 = currentNode;console.log('count', currentNode);}} type="text" />
                        <button onClick={this.showInfo}>click me</button>
                    </div>
                )
            }
        }

        ReactDOM.render(<Demo />, document.getElementById('test'))
    </script>
</body>
</html>
```

![[1702218341818.png]]

createRef

```HTML
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hello React</title>
</head>
<body>
    <div id="test"></div>
    <script type="text/javascript" src="https://unpkg.com/react@17/umd/react.development.js"></script>
    <script type="text/javascript" src="https://unpkg.com/react-dom@17/umd/react-dom.development.js"></script>
    <script type="text/javascript" src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
    <script type="text/javascript" src="https://unpkg.com/prop-types@15.8.1/prop-types.js"></script>
    <script type="text/babel">
        class Demo extends React.Component {
            myRef = React.createRef();

            showData = () => {
                alert(this.myRef.current.value);
            }

            render() {
                return (
                    <div>
                        <input ref={this.myRef} type="text" placeholder="click me" />
                        <button onClick={this.showData}>click left</button>
                    </div>
                )
            }
        }

        ReactDOM.render(<Demo />, document.getElementById('test'));
    </script>
</body>
</html>
```

