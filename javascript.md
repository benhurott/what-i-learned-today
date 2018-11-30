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