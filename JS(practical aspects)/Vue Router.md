hash 模式

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Vue Router</title>
</head>
<body>
    <ul>
        <ul>
            <li><a href="#/home">home</a></li>
            <li><a href="#/about">about</a></li>
            <div id="routeView"></div>
        </ul>
    </ul>
</body>
<script>
    let routerView = routerView;
    window.addEventListener('hashchange', () => {
        let hash = location.hash;
        routerView.innerHTML = hash;
    })
    window.addEventListener('DOMContentLoaded', () => {
        if (!location.hash) location.hash = '/';
        else {
            let hash = location.hash;
            routerView.innerHTML = hash;
        }
    })
</script>
</html>
```

history 模式

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Vue Router</title>
</head>
<body>
    <ul>
        <ul>
            <li><a href="/home">home</a></li>
            <li><a href="/about">about</a></li>
            <div id="routeView"></div>
        </ul>
    </ul>
</body>
<script>
    let routerView = routeView;
    window.addEventListener('DOMContentLoaded', onLoad);
    window.addEventListener('popstate', () => routerView.innerHTML = location.pathname);

    const onLoad = () => {
        routerView.innerHTML = location.pathname;
        let linkList = document.querySelector('a[href]');
        linkList.forEach(el => el.addEventListener('click', e => {
            e.preventDefault();
            history.pushState(null, '', el.getAttribute('href'));
            routerView.innerHTML = location.pathname;
        }))
    }
</script>
</html>
```

Vue Router

```js
let Vue = null;

class HistoryRoute {
  constructor() {
    this.current = null;
  }
}

class VueRouter {
  constructor(options) {
    this.mode = options.mode || "hash";
    this.routes = options.routes || [];
    this.routesMap = this.createMap(this.routes);
    this.history = new HistoryRoute();
    this.init();
  }

  init() {
    if (this.mode === "hash") {
      location.hash ? "" : (location.hash = "/");
      window.addEventListener(
        "load",
        () => (this.history.current = location.hash.slice(1))
      );
      window.addEventListener(
        "hashchange",
        () => (this.history.current = location.hash.slice(1))
      );
    } else {
      location.pathname ? "" : (location.pathname = "/");
      window.addEventListener(
        "load",
        () => (this.history.current = location.pathname)
      );
      window.addEventListener(
        "popstate",
        () => (this.history.current = location.pathname)
      );
    }
  }

  createMap(routes) {
    return routes.reduce((pre, cur) => {
      pre[cur.pathname] = cur.component;
      return pre;
    }, {});
  }
}

VueRouter.install = function (v) {
  Vue = v;
  Vue.mixin({
    beforeCreate() {
      if (this.$options && this.$options.router) {
        this._root = this;
        this._router = this.$options.router;
        Vue.utils.defineReactive(this, "xxx", this._router.history);
      } else {
        this._root = this.$parent && this.$parent._root;
      }
      Object.defineProperty(this, "$router", {
        get() {
          return this._root._router;
        },
      });
      Object.defineProperty(this, "$route", {
        get() {
          return this._root._router.history.current;
        },
      });
    },
  });

  Vue.component("router-link", {
    props: {
      to: String,
    },
    render(h) {
      let mode = this._self._root._router.mode;
      let to = mode === "hash" ? "#" + this.to : this.to;
      return h("a", { attrs: { href: to } }, this.$slots.default);
    },
  });

  Vue.component("router-view", {
    render(h) {
      let current = this._self._root._router.history.current;
      let routeMap = this._self._root._router.routeMap;
      return h(routeMap[current]);
    },
  });
};

export default VueRouter;
```