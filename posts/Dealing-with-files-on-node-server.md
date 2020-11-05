---
title: Dealing with files on Node server
description: How to handle uploading files on Node.js
date: 2020-11-03
tags:
  - javascript
  - backend
  - node.js
  - express
layout: layouts/post.njk
image: https://writejavascript.com/assets/static/node.js-file-upload.d3fc2e6.abee768749161961d275ca3015c5be88.jpg
---

## Upload a file

### Create a form to upload a file

```html
<form action="/upload" enctype ="multipart/form-data" method="post">
      <input type="file" class="file-input" name="photo">
      <input type="submit" value="Submit" class="submit-button" >
</form>
```

`enctype ="multipart/form-data"` 

enctype attribute defines the MIME type of the input and giving it a value of `multipart/form-data` allows a file `<input />` to upload file data

### Handle multipart data

for that task you can use **[Multer npm package](https://www.npmjs.com/package/multer)** which is a middleware for handling multipart data

#### so what exactly Multer does?

Multer adds two objects to the request:

- body: contains the values of the text fields of the form if there is any
- file: contains the file uploaded via the form if using `single` function *uploading one file*

    or

- files: contains the files uploaded via the form if using `array` function *uploading multi files*

Upload single image

```jsx
router.post('/upload', upload.single('photo'), (request, response) => {
	  // request.file
		// request.body
		console.log(request.file);
    if (req.fileValidationError)
      return res.status(400).json({ error: req.fileValidationError });

    return res.status(201).json({ success: true });
}
```

Output for `console.log(request.file)`

```json
{
  fieldname: 'photo',
  originalname: 'photo name.PNG',
  encoding: '7bit',
  mimetype: 'image/png',
  destination: 'api/uploads/',
  filename: 'photo name.PNG',
  path: 'api\\uploads\\photo name.PNG',
  size: 1165007
}
```
Upload multi-images

```jsx
router.post('/upload', upload.array('photo'), (request, response) => {
	  // request.files
		// request.body
		console.log(request.files);
    if (req.fileValidationError)
      return res.status(400).json({ error: req.fileValidationError });

    return res.status(201).json({ success: true });
}
```

Output for `console.log(request.file)`

```json
[
  {
    fieldname: 'photo',
    originalname: '1.jpg',
    encoding: '7bit',
    mimetype: 'image/jpeg',
    destination: 'server/uploads/',
    filename: '1.jpg',
    path: 'server\\uploads\\1.jpg',
    size: 252438
  },
  {
    fieldname: 'photo',
    originalname: '2.gif',
    encoding: '7bit',
    mimetype: 'image/gif',
    destination: 'server/uploads/',
    filename: '2.gif',
    path: 'server\\uploads\\2.gif',
    size: 1511827
  }
]
```

#### How to use Multer?

Create a new instance of Multer passing an object with some options:

```jsx
const upload = multer({
  fileFilter,
  storage,
});
```

- `Storage`||`dest`

    Specifies where Multer should store the files

    To create this you will use `multer.diskStorage` passing an object with some options:

    - `destination`

        Determines the path to the folder the uploaded files will be stored within

    - `filename`

        determines what the file should be named inside the folder

        *if not given the file will be given a random name with no extension*

    PSSST: *if you omit this option, your files will be kept in memory not written on disk*

    ```jsx
    const storage = multer.diskStorage({
      destination: 'api/uploads/',
      filename: filename,
    });

    function filename(request, file, callback) {
      callback(null, file.originalname);
    }
    ```

- `fileFilter`

    Determine which files should be uploaded and which ones should be skipped

    ```jsx
    function fileFilter(request, file, callback) {
      if (file.mimetype !== 'image/png') {
        request.fileValidationError = 'Wrong file type';
        callback(null, false, new Error('Wrong file type'));
      } else {
        callback(null, true);
      }
    }
    ```

OH YAH!!! you did it you uploaded a file 🤛

### Here's the whole peace of magic!✨

```jsx
const express = require("express");
const multer = require("multer");

const app = express();
const port = 3000;

const storage = multer.diskStorage({
  destination: 'api/uploads/',
  filename: filename,
});

const upload = multer({
  fileFilter,
  storage,
});

function filename(request, file, callback) {
  callback(null, file.originalname);
}

function fileFilter(request, file, callback) {
  if (file.mimetype !== 'image/png') {
    request.fileValidationError = 'Wrong file type';
    callback(null, false, new Error('Wrong file type'));
  } else {
    callback(null, true);
  }
}

app.post('/upload', upload.single('photo'), (request, response) => {
    // request.file
    // request.body
    console.log(request.file);
    if (req.fileValidationError)
      return res.status(400).json({ error: req.fileValidationError });

    return res.status(201).json({ success: true });
}

app.listen(port, () => {
  console.log(`App listening at http://localhost:${port}`)
});
```