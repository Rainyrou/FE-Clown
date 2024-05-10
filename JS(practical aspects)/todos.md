```js
import React, { useState } from "react";

function TodosApp() {
  const [todos, setTodos] = useState([]);
  const [input, setInput] = useState("");

  const handleInputChange = (e) => setInput(e.target.value);
  const handleAddTodo = () => {
    if (input.trim() !== "") {
      setTodos([...todos, { id: Date.now(), text: input, completed: false }]);
      setInput("");
    }
  };
  const handleToggleTodo = (id) =>
    setTodos(
      todos.map((todo) =>
        todo.id === id ? { ...todo, completed: !todo.completed } : todo
      )
    );
  const handleDeleteTodo = (id) =>
    setTodos(todos.filter((todo) => todo.id !== id));
  return (
    <div>
      <input type="text" value={input} onChange={handleInputChange} />
      <button onClick={handleAddTodo}>add</button>
      <ul>
        {todos.map((todo) => (
          <li
            key={todo.id}
            style={{ textDecoration: todo.completed ? "line-through" : "none" }}
          >
            {todo.text}
            <button onClick={() => handleToggleTodo(todo.id)}>toggle</button>
            <button onClick={() => handleDeleteTodo(todo.id)}>delete</button>
          </li>
        ))}
      </ul>
    </div>
  );
}

export default TodosApp;
```