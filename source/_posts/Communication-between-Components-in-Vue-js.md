---
title: Communication between Components in Vue.js
date: 2021-06-10 15:43:40
tags: vue.js
categories:
- Web 前端
---

> *In Vue, the parent-child component relationship can be summarized as props down, events up. The parent passes data down to the child via props, and the child sends messages to the parent via events...*

## Pass Data Between Parent and Child Components

### props and $emit

- Using props to share data from parent to child
- Emitting custom events to share data from child to parent

`parent.vue`

```vue
<template>
  <div style="padding: 10px 10px 10px 10px">
    <h2>This is the parent page</h2>
    <div>
      <button @click="onSendClick">Send to Child</button>
    </div>
    <div>
      Received from child by emit: {{receiveFromChild}}
    </div>
    <Child :name="childName" @receiveFromChild="onReceiveFromChild"/>
  </div>
</template>

<script>
import Child from "./Child";
export default {
  name: "Parent",
  components: {
    Child,
  },
  methods: {
    onSendClick() {
      this.childName = Math.random().toString(36).substring(7);
    },
    onReceiveFromChild(value) {
      this.receiveFromChild = value;
    }
  },
  data() {
    return {
      receiveFromChild: undefined,
      childName: undefined
    }
  },
}
</script>
```

`Child.vue`

```vue
<template>
  <div style="background-color: lightgray; padding: 10px 10px 10px 10px">
    <h2>I'm the child page</h2>
    <p>Receive from parent by props: {{name}}</p>
    <button @click="onSendButtonClick">Send to parent</button>
  </div>
</template>

<script>
export default {
  name: "Child",
  props: {
    name: {
      type: String,
      default: "",
    },
  },
  methods: {
    onSendButtonClick() {
      let sendValue = Math.random().toString(36).substring(7) + " " + new Date().toISOString().replace("T", " ").replace("Z", "");
      this.$emit("receiveFromChild", sendValue);
    }
  },
}
</script>
```

### Using Vuex to create an app-level shared state.

## Call Methods between Parent and Child Components

### Call child methods in Parent Components

Call child methods by ref of Vue 2

`parent.vue`

```vue
<div class="form">                          
    <child-component ref="childComponentRef" />                
</div>
```

```vue
this.$refs.childComponentRef.doSomething();
```

### Call parent methods  in Child Components

Call parent methods by props

`parent.vue`

```vue
<div class="form">                          
    <child-component :doSomething="doSomething" />           
</div>
```

`child.vue`

```vue
props: {
    doSomething: {
    	type: Function
    }
},
```

## Summary

Update child components status

- Using props to share data from parent to child. (control child status in parent)
- Call child methods by ref. (control child status directly)

Update parent components status

- Emitting custom events to share data from child to parent. (control parent status in child)
- Call parent methods by props. (control parent status directly)
