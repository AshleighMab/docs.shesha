---
sidebar_label: API for Saving Files
title: API for Saving Files
---

# API for saving files

The `fileSaver` object lets a form script trigger a client-side file download, prompting the user's browser to save a file to their own machine (not "to the backend" as the name might suggest). It is the `file-saver` library's `saveAs` function, exposed directly as a standard script variable alongside `data`, `form`, and `http`.  

---

```typescript
fileSaver.saveAs(data: object | string, filename?: string, options?: object) => void
```
:::note Always pass a Blob
The type signature above also accepts a plain `string` or `object` for `data`, but don't rely on that. Always wrap your content in `new Blob([...])` first, as shown in the examples below - passing a raw string or object can behave inconsistently
from one browser to another, while a `Blob` downloads reliably everywhere.
:::

**Form type to use:** Any form type - `fileSaver` is available everywhere standard script variables are.

**Example - Save an HTTP response as a downloadable file:**

```javascript
const response = await http.get('/api/services/app/Report/Export', { responseType: 'blob' });
fileSaver.saveAs(new Blob([response.data]), 'report.xlsx');
```

**Example - Save JSON data as a downloadable file:**

```javascript
const json = JSON.stringify({ id: data.id, name: data.name }, null, 2);
fileSaver.saveAs(new Blob([json], { type: 'application/json' }), 'record.json');
```
