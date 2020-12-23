---
title: >-
  Web Problems: Clicking a Button in a Form Will Auto Close the Form Page and
  Refresh the Current URL Page
date: 2020-12-23 16:37:28
tags: error
categories: 
- 其它
- 问题解决
---

## Background

In the CRM system, It using [Bootstrap Modal](https://getbootstrap.com/docs/4.0/components/modal/) to construct edit form pages. Originally, It has no form validation, after I take over the project, I want to add form validation. So I add [jQuery Validation Plugin](https://jqueryvalidation.org/) to do form validation. Originally, the form page has no `<form>`, but `<div>`. The jQuery validation plugin need `<form>` to work, so I replace `<div id="myForm">` with `<form id="myForm">`. However, after I added `<form>`, when I click the submit button, Some problems have occurred. The problem is after submit the form, the current URL page auto refresh. This problem confused me. The following code is to submit the form and then close the modal.

```javascript
$("#myForm").find("#submit").off("click").on("click", function () {
	// form validation and submit, after submit success to close modal and refresh table
    ...
});
```

## Error Info

The current URL page auto refresh.

## Solutions

We should override the submit event of the `<form>`, and using [Event.preventDefault()](https://developer.mozilla.org/en-US/docs/Web/API/Event/preventDefault) to prevent the default actions of submit form. (e.g the default action of clicking on a checkbox is toggling a checkbox.)

```javascript
$('#' + formElementId).bind('submit', function (e) {
    e.preventDefault();
	// form validation and submit, after submit success to close modal and refresh table
    ...
});
```

## Reasons

Cause by the default action of submit form.

## References

[1] [Bootstrap Modal](https://getbootstrap.com/docs/4.0/components/modal/)

[2] [Event.preventDefault](https://developer.mozilla.org/en-US/docs/Web/API/Event/preventDefault)