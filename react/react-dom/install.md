# document
https://www.npmjs.com/package/react-dom

https://github.com/facebook/react

# npm i react-dom
npm i react-dom

# code default
```bash
import { createRoot } from 'react-dom/client';

function App() {
  return <div>Hello World</div>;
}

const root = createRoot(document.getElementById('root'));
root.render(<App />);
```