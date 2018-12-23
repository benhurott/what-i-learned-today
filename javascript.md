# Javascript

## Utilities

### Get base64 file size

```js
function getBase64FileSize({
  data,
  contentType = 'image/jpeg',
  sizeType = 'kb',
  decimalPlaces = 2
}) {
  const stringLength = data.length - `data:${contentType};base64,`.length

  const sizeInBytes = 4 * Math.ceil(stringLength / 3) * 0.5624896334383812

  let result

  switch (sizeType) {
    case 'by':
      result = sizeInBytes
      break;
    case 'kb':
      result = sizeInBytes / 1000
      break;
    case 'mb':
      result = sizeInBytes / 1000 / 1000
      break;
    default:
      throw new Error(`The param sizeType (${sizeType}) is not valid.`)
  }

  return +result.toFixed(decimalPlaces)
}
```

### throttle

```js
function throttle(fn, milliseconds) {
  let lastCallTime;

  return function() {
    let nowTime = Date.now();

    if(!lastCallTime || lastCallTime < nowTime - milliseconds) {
      lastCallTime = nowTime;
      fn();
    }
  };
}
```

### Get query string as object

```js
function getQueryStringParameters() {
  const urlParams = new URLSearchParams(window.location.search)

  let result = {}

  const paramKeys = urlParams.keys()

  for (let k of paramKeys) {
    result[k] = decodeURI(urlParams.get(k))
  }

  return result
}
```

### Set object as query string in url

```js
function setObjectAsQueryStringInUrl(obj) {
  if (window.history.pushState) {
    let params = []

    Object.keys(obj).forEach(k => {
      params.push(`${k}=${encodeURI(obj[k])}`)
    })

    const queryString = params.join('&')

    var newurl =
      window.location.protocol +
      '//' +
      window.location.host +
      window.location.pathname +
      `?${queryString}`
    window.history.pushState({ path: newurl }, '', newurl)
  }
}
```
