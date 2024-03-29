# decodePixelsFromOffscreenCanvas

Convert `OffscreenCanvas` to `ImageData` using `decodePixels()` and `zlib.js` from [png.js](https://github.com/foliojs/png.js) (`OffscreenCanvasRenderingContext2D` not implemented at Firefox 70 or Nightly 72).

<h3>Usage</h3>

`Worker`

```
importScripts("decodePixelsFromOffscreenCanvas.js");
// decodePixelsFromOffscreenCanvas: function  
// returns Promise: ImageData

const manipulatePixels = imageData => {
  // do stuff with ImageData
};

onmessage = async e => {
  try {
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

`document`
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

plnkr https://plnkr.co/edit/0By56O?p=preview
