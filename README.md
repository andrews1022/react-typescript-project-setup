# React, Styled Components & TypeScript Setup

This is my personal setup approach for generating a project using the above technologies

1. Create a directory: `mkdir [PROJECT_NAME]`
2. Createa a react-typescript boilerplate project using CRA: `npx create-react-app . --template typescript`
3. After downloading, install styled components packages: `npm i styled-components @styled-components`
4. Go into the src folder: `cd src`
5. Cleanup unnecessary files: `rm App.css App.test.tsx index.css logo.svg reportWebVitals.ts setupTests.ts`
6. All that should be left are `App.tsx`, `index.tsx`, and `react-app-env.d.ts`
7. Copy and paste this code into index.tsx:

```
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';

ReactDOM.render(
	<React.StrictMode>
		<App />
	</React.StrictMode>,
	document.getElementById('root')
);
```

8. Copy and paste this code into App.tsx:

```
import React from 'react';

const App = () => (
	<div>
		<h1>Hello from App.tsx!</h1>
	</div>
);

export default App;

```
