# DragDropTouch fork

Polyfill that enables HTML5 drag drop support on mobile (touch) devices.

## Differences from the original version

- This version workarounds unwanted scrolling issue on iOS Safari, which occurs when `dragThresholdPixels` is set much larger

## How to "install"

```js
import { enableDragDropTouch } from "./drag-drop-touch.esm.min.js";

// Set up the default full page polyfill:
enableDragDropTouch();

// Or, explicitly polyfill only certain elements
enableDragDropTouch(dragRootElement, dropRootElement);

// Or even explicitly polyfill only certain elements with non-default behaviour
const options = {
  // ...
};
enableDragDropTouch(dragRootElement, dropRootElement, options);
```

## Polyfill behaviour

The **DragDropTouch** polyfill attaches listeners to the document's touch events:

- On **touchstart**, it checks whether the target element has the draggable
  attribute or is contained in an element that does. If that is the case, it
  saves a reference to the "drag source" element and prevents the default
  handling of the event.
- On **touchmove**, it checks whether the touch has moved a certain threshold
  distance from the origin. If that is the case, it raises the **dragstart**
  event and continues monitoring moves to fire **dragenter** and **dragleave**.
- On **touchend**, it raises the **dragend** and **drop** events.

To avoid interfering with the automatic browser translation of some touch events
into mouse events, the polyfill performs a few additional tasks:

- Raise the **mousemove**, **mousedown**, **mouseup**, and **click** events when
  the user touches a draggable element but doesn't start dragging,
- Raise the **dblclick** event when there's a new touchstart right after a click,
  and
- Raise the **contextmenu** event when the touch lasts a while but the user doesn't
  start dragging the element.

## Overriding polyfill behaviour

The following options can be passed into the enabling function to change how the
polyfill works:

- **allowDragScroll** is a flag that determines whether to allow scrolling when
  a drag reaches the edges of the screen. This can be either `true` or `false`,
  and is `true` by default.
- **contextMenuDelayMS** is the number of milliseconds we'll wait before the
  polyfill triggers a context menu event on long press. This value is 900 by
  default.
- **dragImageOpacity** determines how see-through the "drag placeholder", that's
  attached to the cursor while dragging, should be. This value is a number in
  the interval [0, 1], where 0 means fully transparent, and 1 means fully opaque.
  This value is 0.5 by default.
- **dragScrollPercentage** is the size of the "hot region" at the edge of the
  screen as a percentage value on which scrolling will be allowed, if the
  **allowDragScroll** flag is true (which is its default value). This value is
  10 by default.
- **dragScrollSpeed** is the number of pixels to scroll if a drag event occurs
  within a scrolling hot region. This value is 10 by default.
- **dragThresholdPixels** is the number of pixels that a touchmove needs to
  actually move before the polyfill switches to drag mode rather than click mode.
  This value is 5 by default
- **forceListen** is a flag that determines whether the polyfill should be
  enabled irrespective of whether the browser indicates that it's running on
  a touch-enabled device or not. This value is `true` by default.

## Thanks

- Thanks to Eric Bidelman for the great tutorial on HTML5 drag and drop:
  [Native HTML5 Drag and Drop](http://www.html5rocks.com/en/tutorials/dnd/basics/).
- Thanks also to Chris Wilson and Paul Kinlan for their article on mouse and touch events:
  [Touch And Mouse](http://www.html5rocks.com/en/mobile/touchandmouse/).
- Thanks to Tim Ruffles for his iOS shim code which was inspiring:
  [iOS DragDrop Shim](https://github.com/timruffles/ios-html5-drag-drop-shim).

## License

[MIT License](./LICENSE)

## For developers

If you wish to work on this library, fork and clone the repository, then run
`npm install` to install all the dependency, followed by a one-time
`npm run dev:setup`, which will install the necessary components for running
the integration tests.

### Running tests

This repository uses the standard `npm test` command to run build and
integration tests. Build testing consists of linting the source code using `tsc`,
auto-formatting it using `prettier`, and compiling it into three bundles (debug, normal, and minified) using `esbuild`. Integration tests are found in the
`tests/touch.spec.js` file, using Playwright as test runner.

Additionally, `npm run test:debug` will run the tests with `DEBUG` statements preserved, useful for when tests fail to pass and you're trying to find out what's actually happening.

### Manual testing

To manually test in the browser, you can run `npm start` and then open the
URL that is printed to the console once the initial build tasks have finished.
This runs a local server that lets you run the demo page, but with the
`drag-drop-touch.esm.min.js` replaced by a `drag-drop-touch.debug.esm.js`
instead, which preserves all debug statements used in the TypeScript source.

To add your own debug statements, use the `DEBUG:` label followed by either
a normal statement, or multiple statements wrapped in a new block.
