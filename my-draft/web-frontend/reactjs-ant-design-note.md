# Ant Design of React

**Content**

- Ant Design of React
- Getting Started



## Ant Design of React

Following the Ant Design specification, we developed a React UI library `antd` that contains a set of high quality components and demos for building rich, interactive user interfaces.

### Features

- 🌈 Enterprise-class UI designed for web applications.
- 📦 A set of high-quality React components out of the box.
- 🛡 Written in TypeScript with predictable static types.
- ⚙️ Whole package of design resources and development tools.
- 🌍 Internationalization support for dozens of languages.
- 🎨 Powerful theme customization in every detail.

### Installation

```
npm install antd
```

or

```
yarn add antd
```

### Usage

```
import { DatePicker } from 'antd';

ReactDOM.render(<DatePicker />, mountNode);
```

And import stylesheets manually:

```jsx
import 'antd/dist/antd.css'; // or 'antd/dist/antd.less'
```



## Getting Started

- Following the [Install and Initialization](https://ant.design/docs/react/use-with-create-react-app#Install-and-Initialization) section of "Use in create-react-app"

- Import Ant Design components and CSS:

  ```
  import { DatePicker, message } from 'antd';
  import 'antd/dist/antd.css';
  ```

- Run project

  ```
  yarn start
  # or
  npm run start
  ```

  

Example: 

Replace the content of `/src/index.js` as follows:

```react
import React, { useState } from 'react';
import { render } from 'react-dom';
import { DatePicker, message } from 'antd';
import 'antd/dist/antd.css';
import './index.css';

const App = () => {
  const [date, setDate] = useState(null);
  const handleChange = value => {
    message.info(`Selected Date: ${value ? value.format('YYYY-MM-DD') : 'None'}`);
    setDate(value);
  };
  return (
    <div style={{ width: 400, margin: '100px auto' }}>
      <DatePicker onChange={handleChange} />
      <div style={{ marginTop: 16 }}>
        Selected Date: {date ? date.format('YYYY-MM-DD') : 'None'}
      </div>
    </div>
  );
};

render(<App />, document.getElementById('root'));
```



## Use in create-react-app

[create-react-app](https://github.com/facebookincubator/create-react-app) is one of the best React application development tools. We are going to use `antd` within it and modify the webpack config for some customized needs.

### Install and Initialization

Before all start, you may need install [yarn](https://github.com/yarnpkg/yarn/).

```bash
$ yarn create react-app antd-demo

# or

$ npx create-react-app antd-demo
```

Then we go inside `antd-demo` and start it.

```bash
$ cd antd-demo
$ yarn start
```

Open the browser at http://localhost:3000/. It renders a header saying "Welcome to React" on the page.

### Import antd

Below is the default directory structure.

```null
├── README.md
├── package.json
├── public
│   ├── favicon.ico
│   └── index.html
├── src
│   ├── App.css
│   ├── App.js
│   ├── App.test.js
│   ├── index.css
│   ├── index.js
│   └── logo.svg
└── yarn.lock
```

Now we install `antd` from yarn or npm.

```bash
$ yarn add antd
```

Modify `src/App.js`, import Button component from `antd`.

```jsx
import React from 'react';
import { Button } from 'antd';
import './App.css';

const App = () => (
  <div className="App">
    <Button type="primary">Button</Button>
  </div>
);

export default App;
```

Add `antd/dist/antd.css` at the top of `src/App.css`.

```css
@import '~antd/dist/antd.css';
```

Ok, you should now see a blue primary button displayed on the page. Next you can choose any components of `antd` to develop your application. 



## References

[1] [Ant Design](https://ant.design/docs/react/introduce)