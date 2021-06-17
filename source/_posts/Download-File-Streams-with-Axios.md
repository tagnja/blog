---
title: Download File Streams with Axios
date: 2021-06-17 21:21:10
tags: js
categories:
- Web 前端
---

Download file streams with Axios

```javascript
const axios = require('axios').default;

download() {
    let downloadUri = '/api/download';
    let params = {};
    axios.get(downloadUri, {
      	params:params,
      	responseType: 'blob',
    }).then((response) => {
        const headerval = response.headers['content-disposition'];
        let filename = headerval.split(';')[1].split('=')[1].replace('"', '').replace('"', '');
        filename = decodeURI(filename);
      	const url = window.URL.createObjectURL(new Blob([response.data]));
        const link = document.createElement('a');
        link.href = url;
        link.setAttribute('download', filename);
        document.body.appendChild(link);
        link.click();
    });
}
```

Notice: 

- `responseType: 'blob'`
- `filename = decodeURI(filename)`
