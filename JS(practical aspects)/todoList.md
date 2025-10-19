```js
import React, { useState } from "react";

function TodoList() {
  const [input, setInput] = useState("");
  const [todos, setTodos] = useState([]);

  const handleInput = (e) => setInput(e.target.value);
  const addTodo = () => {
    if (input.trim() !== "") {
      setTodos([...todos, { id: Date.now(), text: input, completed: false }]);
      setInput("");
    }
  };
  const deleteTodo = (id) => setTodos(todos.filter((todo) => todo.id !== id));
  const toggleTodo = (id) =>
    setTodos(
      todos.map((todo) =>
        todo.id === id ? { ...todo, completed: !todo.completed } : todo
      )
    );

  return (
    <div className="max-w-md mx-auto mt-10 p-5 bg-white shadow-lg rounded-lg">
      <h1 className="text-2xl font-bold mb-4">Todo List</h1>
      <div className="flex mb-4">
        <input
          type="text"
          value={input}
          onChange={handleInput}
          className="flex-grow p-2 border rounded-l-md focus:outline-none"
          placeholder="Add a new task"
        />
        <button
          onClick={addTodo}
          className="p-2 bg-blue-500 text-white rounded-r-md hover:bg-blue-600"
        >
          Add
        </button>
      </div>
      <ul>
        {todos.map((todo) => (
          <li
            key={todo.id}
            className={`flex justify-between items-center p-2 border-b ${
              todo.completed ? "line-through text-gray-500" : ""
            }`}
          >
            {todo.text}
            <div>
              <button
                onClick={() => toggleTodo(todo.id)}
                className="p-1 bg-green-500 text-white rounded-md mr-2 hover:bg-green-600"
              >
                Toggle
              </button>
              <button
                onClick={() => deleteTodo(todo.id)}
                className="p-1 bg-red-500 text-white rounded-md hover:bg-red-600"
              >
                Delete
              </button>
            </div>
          </li>
        ))}
      </ul>
    </div>
  );
}

export default TodoList;
```