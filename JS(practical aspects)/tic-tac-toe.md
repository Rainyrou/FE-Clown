[Installation - Tailwind CSS](https://tailwindcss.com/docs/installation)

`App.js`

```js
import React, { useState } from "react";
import "./index.css";

function App() {
  const [squares, setSquares] = useState(new Array(9).fill(null));
  const [xIsNext, setXIsNext] = useState(true);

  const calculateWinner = (squares) => {
    const lines = [
      [0, 1, 2],
      [3, 4, 5],
      [6, 7, 8],
      [0, 3, 6],
      [1, 4, 7],
      [2, 5, 8],
      [0, 4, 8],
      [2, 4, 6],
    ];
    for (let i = 0; i < lines.length; ++i) {
      const [a, b, c] = lines[i];
      if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c])
        return squares[a];
    }
    return null;
  };

  const winner = calculateWinner(squares);
  const status = winner
    ? `Winner: ${winner}`
    : `Next Player ${xIsNext ? "X" : "O"}`;

  const renderSquare = (i) => {
    return (
      <button
        className="flex items-center justify-center w-16 h-16 border-2 bg-white border-gray-300 text-xl font-bold"
        onClick={() => handleClick(i)}
      >
        {squares[i]}
      </button>
    );
  };

  const handleClick = (i) => {
    const clonedSquares = squares.slice();
    if (calculateWinner(squares) || squares[i]) return;
    clonedSquares[i] = xIsNext ? "X" : "O";
    setSquares(clonedSquares);
    setXIsNext(!xIsNext);
  };

  return (
    <div className="flex flex-col items-center mt-20">
      <div className="grid grid-cols-3 gap-1 p-2 border-4 border-dark rounded-lg bg-gray-200">
        {Array.from({ length: 9 }, (_, i) => renderSquare(i))}
      </div>
      <div className="mt-4 text-xl">
        <div>{status}</div>
        <button
          className="px-4 py-2 mt-2 rounded shadow bg-blue-500 text-white"
          onClick={() => setSquares(new Array(9).fill(null))}
        >
          Reset Game
        </button>
      </div>
    </div>
  );
}

export default App;
```