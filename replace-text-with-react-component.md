# Replace text with React Component

```javascript
import React from 'react';
import parse from 'html-react-parser';

let html =
  '\u003Ch2\u003ETest H2\u003C/h2\u003E\n\u003Cp\u003Etest p tag \u003Ca href="https://www.test.com/" target="_self"\u003Eaccount team\u003C/a\u003E.&nbsp;\u003C/p\u003E\n';

export default function App() {
  const handleClick = () => {
    console.log('clicked');
  };

  return (
    <>
      <div>
        {parse(html, {
          replace(domNode) {
            if (domNode.name == 'a') {
              console.log(domNode);
              return <button onClick={handleClick}>replaced button</button>;
            }
          },
        })}
      </div>
    </>
  );
}

```
