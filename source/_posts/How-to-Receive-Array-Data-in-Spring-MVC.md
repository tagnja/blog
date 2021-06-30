---
title: How to Receive Array Data in Spring MVC
date: 2021-06-30 11:27:52
tags: Spring
categories: 
- Java
- Java Web
---

## Receive from Request URL Query String

### Integer Array

Frontend Pass with HTTP request

- url?ids=1,2,3
- url?ids=1&ids=2&ids=3

Backend receive in controller methods

- `Integer[] ids`
- `@RequestParam Integer[] ids`
- `@RequestParam List<Integer> ids`

### String Array

Frontend Pass with HTTP request

- url?ids=a,b,c
- url?ids=a&ids=b&ids=c

Backend receive in controller methods

- `String[] ids`
- `@RequestParam String[] ids`
- `@RequestParam List<String> ids`

## Receive from request body contentType=multipart/form-data

### Integer Array

Frontend Pass with HTTP request

- ids=1,2,3
- ids=1, ids=2, ids=3

Backend receive in controller methods

- `Integer[] ids`
- `@RequestParam Integer[] ids`
- `@RequestParam List<Integer> ids`

### String Array

Frontend Pass with HTTP request

- ids=a,b,c
- ids=a, ids=b, ids=c


Backend receive in controller methods

- `String[] ids`
- `@RequestParam String[] ids`
- `@RequestParam List<String> ids`

## Receive from request body contentType=application/json

### Integer Array

Frontend Pass with HTTP request

- [1,2,3]

Backend receive in controller methods

- `@RequestBody Integer[] ids`
- `@RequestBody List<Integer> ids`

### String Array

Frontend Pass with HTTP request

- ["a","b","c"]


Backend receive in controller methods

- `@RequestBody String[] ids`
- `@RequestBody List<String> ids`