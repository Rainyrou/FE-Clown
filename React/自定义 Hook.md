自定义 Hook 用于复用不同组件间的状态逻辑，它必须以 use 开头，明确身份，被 React linter 插件正确识别。它的命名应当清晰反映其用途，如 `useFormInput` 或 `useWindowWidth`。创建自定义 Hook 实质上就是创建一个普通的 JavaScript 函数，它可调用其他 Hook并返回一些值供组件使用

```jsx
function useInput(initialValue) {
  const [value, setValue] = useState(initialValue);

  function handleChange(e) {
    setValue(e.target.value);
  }

  return {
    value,
    onChange: handleChange,
  };
}
```

```jsx
function Form() {
  const name = useInput('');
  const age = useInput(0);

  return (
    <form>
      <input
        type="text"
        placeholder="Name"
        {...name} 
      />
      <input
        type="number"
        placeholder="Age"
        {...age} 
      />
    </form>
  );
}
```