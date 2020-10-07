# Vue.js v3 Essentials Note

> Vue.js v3 release date: 18 September 2020

**Content**

- [x] Introduction
- [x] Application & Component Instances
- [x] Template Syntax
- [x] Data Properties and Methods
- Computed Properties and Watchers
- Class and Style Bindings
- Conditional Rendering 
- List Rendering
- Event Handling
- Form Input Bindings
- Components Basics



## Introduction

### What is Vue.js?

> Vue (pronounced /vjuː/, like **view**) is a **progressive framework** for building user interfaces. Unlike other monolithic frameworks, Vue is designed from the ground up to be incrementally adoptable. The core library is focused on the view layer only, and is easy to pick up and integrate with other libraries or existing projects.

### Installation

**Vue Devtools**

When using Vue, we recommend also installing the [Vue Devtools](https://github.com/vuejs/vue-devtools#vue-devtools) in your browser, allowing you to inspect and debug your Vue applications in a more user-friendly interface.

**CDN**

For prototyping or learning purposes, you can use the latest version with:

```html
<script src="https://unpkg.com/vue@next"></script>
```

For production, we recommend linking to a specific version number and build to avoid unexpected breakage from newer versions.

**NPM**

NPM is the recommended installation method when building large scale applications with Vue. It pairs nicely with module bundlers such as [Webpack](https://webpack.js.org/) or [Rollup](https://rollupjs.org/). Vue also provides accompanying tools for authoring [Single File Components](https://v3.vuejs.org/guide/single-file-component.html).

```bash
# latest stable
$ npm install vue@next
```

**CLI**

Vue provides an [official CLI](https://github.com/vuejs/vue-cli) for quickly scaffolding ambitious Single Page Applications. It provides batteries-included build setups for a modern frontend workflow. It takes only a few minutes to get up and running with hot-reload, lint-on-save, and production-ready builds. See [the Vue CLI docs](https://cli.vuejs.org/) for more details.

For Vue 3, you should use Vue CLI v4.5 available on `npm` as `@vue/cli`. To upgrade, you need to reinstall the latest version of `@vue/cli` globally:

```bash
yarn global add @vue/cli
# OR
npm install -g @vue/cli
```

Then in the Vue projects, run

```bash
vue upgrade --next
```

**Vite**

[Vite](https://github.com/vitejs/vite) is a web development build tool that allows for lighting fast serving of code due its native ES Module import approach.

Vue projects can quickly be set up with Vite by running the following commands in your terminal.

With NPM:

```bash
$ npm init vite-app <project-name>
$ cd <project-name>
$ npm install
$ npm run dev
```

Or with Yarn:

```bash
$ yarn create vite-app <project-name>
$ cd <project-name>
$ yarn
$ yarn dev
```

### Declarative Rendering

**Template Render Data: text interpolation**

Using template syntax to declaratively render data to DOM:

```
{{message}}
```

Example: 

```html
<div id="counter">
  Counter: {{ counter }}
</div>

<script src="https://unpkg.com/vue@next"></script>

<script type="text/javascript">
    const Counter = {
      data() {
        return {
          counter: 0
        }
      }
    }

    Vue.createApp(Counter).mount('#counter')
</script>
```

**`v-bind` directive: to set element attribute**

```
<input v-bind:value="message" />
```

### handling user Input

**`v-on` directive: to attach event listeners**

```
<button v-on:click="reverseMessage">Reverse Message</button>
```

Example:

```html
<div id="event-handling">
  <p>{{ message }}</p>
  <button v-on:click="reverseMessage">Reverse Message</button>
</div>

<script src="https://unpkg.com/vue@next"></script>

<script type="text/javascript">
    const EventHandling = {
      data() {
        return {
          message: 'Hello Vue.js!'
        }
      },
      methods: {
        reverseMessage() {
          this.message = this.message
            .split('')
            .reverse()
            .join('')
        }
      }
    }

    Vue.createApp(EventHandling).mount('#event-handling')
</script>
```

 **`v-model` directive: makes two-way binding**

```
<p>{{ message }}</p>
<input v-model="message" />
```

### Conditionals and Loops

**`v-if` directive**

```
<span v-if="seen">Now you see me</span>
```

**`v-for` directive**

```
<ol>
    <li v-for="todo in todos">
    	{{ todo.text }}
    </li>
</ol>
```

### Composing with Components

Usage

```html
// Create Vue application
const app = Vue.createApp(...)

// Define a new component called todo-item
app.component('todo-item', {
  template: `<li>This is a todo</li>`
})

// Mount Vue application
app.mount(...)
```

Example: Basic directive vs components

Basic directive implementation

```html
<div id="list-rendering">
  <ol>
    <li v-for="todo in todos">
      {{ todo.text }}
    </li>
  </ol>
</div>

<script src="https://unpkg.com/vue@next"></script>

<script type="text/javascript">
    const ListRendering = {
      data() {
        return {
          todos: [
            { text: 'Learn JavaScript' },
            { text: 'Learn Vue' },
            { text: 'Build something awesome' }
          ]
        }
      }
    }

    Vue.createApp(ListRendering).mount('#list-rendering')
</script>
```

components implementation

```html
<div id="todo-list-app">
  <ol>
    <todo-item
      v-for="item in groceryList"
      v-bind:todo="item"
      v-bind:key="item.id"
    ></todo-item>
  </ol>
</div>

<script src="https://unpkg.com/vue@next"></script>

<script type="text/javascript">
    const TodoList = {
      data() {
        return {
          groceryList: [
            { id: 0, text: 'Vegetables' },
            { id: 1, text: 'Cheese' },
            { id: 2, text: 'Whatever else humans are supposed to eat' }
          ]
        }
      }
    }

    const app = Vue.createApp(TodoList)

    app.component('todo-item', {
      props: ['todo'],
      template: `<li>{{ todo.text }}</li>`
    })

    app.mount('#todo-list-app')
</script>
```





## Application & Component Instances

### Creating an Application Instance

Every Vue application starts by creating a new **application instance** with the `createApp` function:

```js
const app = Vue.createApp({ /* options */ })
```

The application instance is used to register 'globals' that can then be used by components within that application.

```
const app = Vue.createApp({})
app.component('SearchInput', SearchInputComponent)
app.directive('focus', FocusDirective)
app.use(LocalePlugin)
```

Most of the methods exposed by the application instance return that same instance, allowing for chaining:

```
Vue.createApp({})
  .component('SearchInput', SearchInputComponent)
  .directive('focus', FocusDirective)
  .use(LocalePlugin)
```

Application API

- createApp({<options>})
- component(<name>, <function>|<object>)
- config({<object>})
- directive(<name>, <function>|<object>)
- mixin(<object>)
- mount(<elementId>)
- provide(<key>, <value>)
- unmount(<elementId>)
- use(<function>|<object>)

### The Root Component of Application

The options passed to `createApp` are used to configure the **root component**. That component is used as the starting point for rendering when we **mount** the application.

```
const RootComponent = { /* options */ }
const app = Vue.createApp(RootComponent)
const vm = app.mount('#app')
```

### Component Instance Properties

There are various component options that add user-defined properties to the component instance, such as `data`, `methods`, `props`, `computed`, `inject` and `setup`.

```
const app = Vue.createApp({
  data() {
    return { count: 4 }
  }
})

const vm = app.mount('#app')
```

### Lifecycle Hooks

Each component instance goes through a series of initialization steps when it's created - for example, it needs to set up data observation, compile the template, mount the instance to the DOM, and update the DOM when data changes. Along the way, it also runs functions called **lifecycle hooks**, giving users the opportunity to add their own code at specific stages.

For example, the [created](https://v3.vuejs.org/api/options-lifecycle-hooks.html#created) hook can be used to run code after an instance is created:

```js
Vue.createApp({
  data() {
    return { count: 1 }
  },
  created() {
    // `this` points to the vm instance
    console.log('count is: ' + this.count) // => "count is: 1"
  }
})
```

There are hooks which will be called at different stages of the instance's lifecycle, such as created, [mounted](https://v3.vuejs.org/api/options-lifecycle-hooks.html#mounted), [updated](https://v3.vuejs.org/api/options-lifecycle-hooks.html#updated), and [unmounted](https://v3.vuejs.org/api/options-lifecycle-hooks.html#unmounted). 

### Lifecycle Diagram

![Instance Lifecycle](vuejs-v3-essentials-instance-lifecycle.png)

## Template Syntax

### Interpolations

**Text**

the "Mustache" syntax (double curly braces)

```
<span>Message: {{ msg }}</span>
```

**Raw HTML**

the `v-html` directive

```
<p>Using v-html directive: <span v-html="rawHtml"></span></p>
```

**Attributes**

Mustaches cannot be used inside HTML attributes. Instead, use a [`v-bind` directive](https://v3.vuejs.org/api/#v-bind):

```
<div v-bind:id="dynamicId"></div>
<button v-bind:disabled="isButtonDisabled">Button</button>
```

**Using JavaScript Expressions**

TODO

### Directives

**Arguments**

Some directives can take an "argument", denoted by a colon after the directive name. For example, the `v-bind` directive is used to reactively update an HTML attribute:

```html
<a v-bind:href="url"> ... </a>
```

**Dynamic Arguments**

It is also possible to use a JavaScript expression in a directive argument by wrapping it with square brackets:

```html
<a v-bind:[attributeName]="url"> ... </a>
```

**Modifiers**

Modifiers are special postfixes denoted by a dot, which indicate that a directive should be bound in some special way. For example, the `.prevent` modifier tells the `v-on` directive to call `event.preventDefault()` on the triggered event:

```html
<form v-on:submit.prevent="onSubmit">...</form>
```

### Shorthands

The `v-` prefix serves as a visual cue for identifying Vue-specific attributes in your templates. This is useful when you are using Vue.js to apply dynamic behavior to some existing markup, but can feel verbose for some frequently used directives. At the same time, the need for the `v-` prefix becomes less important when you are building a [SPA](https://en.wikipedia.org/wiki/Single-page_application), where Vue manages every template. Therefore, Vue provides special shorthands for two of the most often used directives, `v-bind` and `v-on`:

**`v-bind` Shorthand**

```
<!-- full syntax -->
<a v-bind:href="url"> ... </a>

<!-- shorthand -->
<a :href="url"> ... </a>

<!-- shorthand with dynamic argument -->
<a :[key]="url"> ... </a>
```

**`v-on` Shorthand**

```html
<!-- full syntax -->
<a v-on:click="doSomething"> ... </a>

<!-- shorthand -->
<a @click="doSomething"> ... </a>

<!-- shorthand with dynamic argument -->
<a @[event]="doSomething"> ... </a>
```



## Data Properties and Methods

### Data Properties

The `data` option for a component is a function. Vue calls this function as part of creating a new component instance. It should return an object, which Vue will then wrap in its reactivity system and store on the component instance as `$data`. For convenience, any top-level properties of that object are also exposed directly via the component instance:

```
const app = Vue.createApp({
  data() {
    return { count: 4 }
  }
})

const vm = app.mount('#app')

console.log(vm.$data.count) // => 4
console.log(vm.count)       // => 4

// Assigning a value to vm.count will also update $data.count
vm.count = 5
console.log(vm.$data.count) // => 5

// ... and vice-versa
vm.$data.count = 6
console.log(vm.count) // => 6
```

### Methods

To add methods to a component instance we use the `methods` option. This should be an object containing the desired methods:

```js
const app = Vue.createApp({
  data() {
    return { count: 4 }
  },
  methods: {
    increment() {
      // `this` will refer to the component instance
      this.count++
    }
  }
})

const vm = app.mount('#app')

console.log(vm.count) // => 4

vm.increment()

console.log(vm.count) // => 5
```

Vue automatically binds the `this` value for `methods` so that it always refers to the component instance. This ensures that a method retains the correct `this` value if it's used as an event listener or callback. You should avoid using arrow functions when defining `methods`, as that prevents Vue from binding the appropriate `this` value.

the `methods` are accessible from within the component's template.

```html
<button @click="increment">Up vote</button>
```

It is also possible to call a method directly from a template.

```
<span :title="toTitleDate(date)">
  {{ formatDate(date) }}
</span>
```

**Debouncing and Throttling**

Vue doesn't include built-in support for debouncing or throttling but it can be implemented using libraries such as [Lodash](https://lodash.com/)

In cases where a component is only used once, the debouncing can be applied directly within `methods`:

```html
<script src="https://unpkg.com/lodash@4.17.20/lodash.min.js"></script>
<script>
  Vue.createApp({
    methods: {
      // Debouncing with Lodash
      click: _.debounce(function() {
        // ... respond to click ...
      }, 500)
    }
  }).mount('#app')
</script>
```

## Computed Properties and Watchers





## Class and Style Bindings

## Conditional Rendering 

## List Rendering

## Event Handling

## Form Input Bindings

## Components Basics





## References

[1] [Vue.js 3.0 Docs](https://v3.vuejs.org/guide/introduction.html)