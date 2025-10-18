将自定义 Hook 状态通过 Context 机制于组件树中共享

```jsx
import { createContext, useContext } from "react";

const useStore = () => {
  const [count, setCount] = useState(0);
  return { count, setCount };
};

const createModel = (hook) => {
  const Context = createContext();
  const Provider = ({ children }) => {
    const value = hook();
    return <Context.Provider value={value}>{children}</Context.Provider>;
  };
  const useContextCostom = useContext(Context);
  return { Provider, useContext: useContextCostom };
};

const Store = createModel(useStore);

export const Demo = () => {
  const { count } = Store.useContext();
  return (
    <Store.Provider>
      <div>{count}</div>
      <AddCountButton />
    </Store.Provider>
  );
};

export const AddCountButton = () => {
  const { setCount } = Store.useContext();
  const onClick = () => setCount();
  return (
    <div>
      <button onClick={onClick}>+1</button>
    </div>
  );
};
```