---
title: >-
  React Ant Design Table Warning: Each child in a list should have a unique key
  prop
date: 2021-01-19 13:30:53
tags:
---

## Background

I using Ant Design Table component to accomplish my list page. I return list data to frontend, but I got some error message in browser console.

## Error Info

```
Warning: Each child in a list should have a unique "key" prop.
Solution
```

## Solutions

Add a field "key" to each objects of the list

```
[{
      key: 1,
      createTime: "2021-01-07 11:13:00",
      numberOfTopUp: 100,
      totalNumber: 100
    },
    ...
]
```

## Reasons

Ant design defined this usage.

## References

[1] [Ant Design Components - Table](https://ant.design/components/table/)