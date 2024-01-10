`index.js`

```JavaScript
import React from "react";
import ReactDOM from "react-dom";
import App from "./App";

ReactDOM.render(<App />, document.getElementById("root"));
```

`App.jsx`

```JSX
import React, { Component } from "react";
import Hello from "./components/Hello";

export default class App extends Component {
  render() {
    return (
      <div>
        <Hello />
      </div>
    );
  }
}
```

`src/components/Hello/index.jsx`

```JSX
import React, { Component } from "react";
import hello from "./index.module.css";

export default class Hello extends Component {
  render() {
    return <h2 className={hello.title}>Hello React</h2>;
  }
}
```

`src/components/Hello/index.module.css`

```CSS
.title {
    background-color: orange;
}
```