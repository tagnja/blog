---
title: Download Static Files in Modern JS
date: 2021-06-11 13:45:20
tags: JS
categories:
- Web 前端
---

1. Put files into `/public` directory

2. Write your download link

Download `/public/pdf/instruction.pdf` 

```vue
<a href="/pdf/instruction.pdf" download>download instruction</a>
```

Preview and download `/public/pdf/instruction.pdf` 

```vue
<a href="/pdf/instruction.pdf" target="_blank">download instruction</a>
```

