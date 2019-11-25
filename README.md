# decodePixelsFromOffscreenCanvas
<h3>Convert OffscreenCanvas to ImageData using png.js<h3>

`OffscreenCanvasRenderingContext2D` is not defined at Firefox 70 or Nightly 72.

Convert `OffscreenCanvas` to `ImageData` using `decodePixels()` and `zlib.js` from [png.js](https://github.com/foliojs/png.js).

<h3>Usage</h3>

In `Worker`

```
importScripts("decodePixelsFromOffscreenCanvas.js");
// decodePixelsFromOffscreenCanvas: function  
// returns Promise: ImageData

const manipulatePixels = imageData => {
  // do stuff with ImageData
};

onmessage = async e => {
  const request = await (await fetch(dataURL)).blob();
  const bitmap = await createImageBitmap(request);
  const {width, height} = bitmap;
  const osc = new OffscreenCanvas(width, height);
  const osctx = osc.getContext("bitmaprenderer");
  osctx.transferFromImageBitmap(bitmap);
  const imageData = await decodePixelsFromOffscreenCanvas(osc);
  manipulatePixels(imageData);
  postMessage({imageData}, [imageData.data.buffer]);
  } catch (e) {
    console.error(e);
  }
};
```

In `document`
```
const canvas = document.querySelector("canvas");
const ctx = canvas.getContext("2d");
const worker = new Worker("worker.js");
worker.onmessage = async e => {
  const {imageData} = e.data;
  canvas.width = imageData.width;
  canvas.height = imageData.height;
  ctx.putImageData(imageData, 0, 0);
  // const bitmap = await createImageBitmap(imageData);
  // ctx.drawImage(bitmap, 0, 0);
  // bitmap.close();
};
worker.postMessage(null);
```
