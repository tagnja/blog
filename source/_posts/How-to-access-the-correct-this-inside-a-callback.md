---
title: How to Access the Correct this Inside a Callback
date: 2021-06-11 13:50:00
tags: JS
categories:
- Web 前端
---

Here are several ways to access parent context inside child context.

1. Use ES6 [Arrow functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions).

An arrow function does not have its own bindings to `this` or `super`, `this` is inherited from the scope. In regular function, `this` is the function itself (it has its own scope).

```vue
getData() {
	ajaxRequest(query).then(response => {
        this.data = response.data;
    });
}
```

2. Store reference to context/this inside another variable, If you can't use ES6 syntax.

```vue
getData() {
	let self = this;
	ajaxRequest(query).then(function(response) {
        self.data = response.data;
    });
}
```

## References

[1] [Arrow function expressions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)

[2] [How to access the correct `this` inside a callback?](https://stackoverflow.com/questions/20279484/how-to-access-the-correct-this-inside-a-callback)
