---
sidebar_label: API for Saving Files
title: API for Saving Files
---

# API for Saving Files

The `fileSaver` variable lets a form script trigger a browser file download, for example to export data the user is currently viewing as a `.csv`, `.json`, or `.zip` file. It is available in any script alongside the other standard script variables (`data`, `form`, `http`, and so on).

---

## fileSaver.saveAs

`fileSaver` is the [file-saver](https://www.npmjs.com/package/file-saver) library, exposed directly to your scripts. Its one method is:

```typescript
fileSaver.saveAs(data: Blob, filename?: string, options?: object): void
```

:::note Always pass a Blob
The type signature above also accepts a plain `string` or `object` for `data`, but don't rely on that. Always wrap your content in `new Blob([...])` first, as shown in the examples below - passing a raw string or object can behave inconsistently
from one browser to another, while a `Blob` downloads reliably everywhere.
:::

**Form type to use:** Any form type - `fileSaver` is available everywhere standard script variables are.

**Example - Download the current form's data as a JSON file:**

```javascript
const content = JSON.stringify(data, null, 2);
fileSaver.saveAs(new Blob([content], { type: 'application/json' }), 'form-data.json');
```

**Example - Save a file already downloaded from the server:**

```javascript
const response = await http.get('/api/services/app/Report/Export', { responseType: 'arraybuffer' });
fileSaver.saveAs(new Blob([response.data]), 'report.xlsx');
```
