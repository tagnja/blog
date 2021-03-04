---
title: Java Management System Core Functions
date: 2020-11-17 20:47:29
tags: system
categories: 
- Java
- Java Web
---

## Backend

### Global Functions

- Class hierarchy design.
- Authentication and authorization. Sign in and sign out. Session Expiration Time.
- Exception handling. Error message encapsulation.
- Method logging with AOP and annotations.
- File upload and download.
- Filter error request.
- Enable CORS(Cross-Origin Resource Sharing) on server-side.
- User, role, privilege. Data privilege and operation privilege.
- I18n (database value, Java string value, JSP element value)
- Prevent duplicate form submission.
-  Using multiple data sources.
-  Scheduling Tasks.
-  Push messages to client browser pages. (implemented by WebSocket or Ajax loop call)

### Module Functions

- Unit tests.
- RESTful API. CRUD.
- Request parameters to POJO. E.g. spring framework`@ModelAttribute`, `@RequestBody`.
- Data validation. E.g. spring framework`@Validated`).
- Type conversion. E.g. spring framework `@DateTimeFormat` for datetime, `@JsonSerialize` for enumeration).
- Data formatting. E.g. spring framework `@JsonFormat` for datetime, `@JsonValue` form enumeration).
- Service transaction.
- Persistence. CRUD.
- Data statistics.
- Data import from Excel file and export to Excel file.

### Others

- HTTPS. 
- Reverse Proxy Server.
- Cache.
- MySQL optimization. Indexing, optimizing schema and SQL.
- JVM optimization.
- DevOps. Git Flow, code review, test, package, deployment.

## Frontend

### Global Functions

- Website framework
  - Top bar `<header>`. (Logo, website name, user center link, login/logout)
  - Horizontal navigation bar `<nav>`.
  - Left vertical sidebar menu `<aside>`.
  - Breadcrumb.
  - Content area `<main>`, `<section>`, `<article>`. (Search, Table and pagination, Form)
  - Bottom footer `<footer>`.
- Utility JS
  - Go to pages.
  - Send CRUD HTTP requests. Submit form.
  - Initialize operation privilege.
  - Load components.
  - Others.
- File structure. Separated index, edit, view page.
- Prevent duplicate form submission.
- Alert success and error message.
- Send CORS Ajax request with jQuery.
- Push browser notifications.

### Module Functions

- List page
  - Initialize operation privilege.
  - Initialize index page. Display breadcrumb, load search area elements (input, select, date time picker), display operation buttons row, load table and pagination, additionally, may load left area tree. 
  - Index page add, edit, delete, search, import/export buttons bind events.
  - Search. Send request with form serialize or FormData.
  - Delete rows.
- Edit page
  - Initialize edit page title, form elements, and submit button. Form elements: input, radio, checkbox, select, select picker, file, rich text editor, date time picker, and input tags.
  - Add edit page bind events.
  - Form data validation. Show error messages.
  - Save or update. Submit form, send request with FormData or JSON.
- Other page
  - Statistical charts.
  - Complex page layout and  style.

## References

[Semantic Elements in HTML](https://www.w3schools.com/html/html5_semantic_elements.asp)