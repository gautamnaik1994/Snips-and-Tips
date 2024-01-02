# Throttle Debounce

```javascript
import { useEffect, useState } from "react";

const throttleFunction = (func, delay) => {
  let lastCall = 0;
  return function () {
    let currentTime = new Date().getTime();
    if (currentTime - lastCall > delay) {
      func();
      lastCall = currentTime;
    }
  };
};

export default function App() {
  const [text, setText] = useState("");
  const handleClick = throttleFunction(() => {
    console.log("clicked");
  }, 1000);

  useEffect(() => {
    let id = setTimeout(() => {
      console.log("Do api call " + text);
    }, 2000);
    return () => {
      clearTimeout(id);
    };
  }, [text]);

  const resizeListener = throttleFunction(() => {
    console.log("resize");
  }, 2000);

  useEffect(() => {
    window.addEventListener("resize", resizeListener);
    return () => {
      window.removeEventListener("resize", resizeListener);
    };
  }, []);

  return (
    <div className="App">
      <input onChange={(e) => setText(e.target.value)} />
      <div>{text}</div>
      <button onClick={handleClick}> Test Throttle </button>
    </div>
  );
}


```
