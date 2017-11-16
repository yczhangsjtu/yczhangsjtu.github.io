---
title: Event
---

# Event

## Event in Javascript

An event handler is set up by adding a listener to a specific event.

```javascript
addEventListener("click", function(){
  console.log("Clicked!");
});
```

Each handler is associated with a context. Each DOM element has its own `addEventListener` method. When called without a DOM element, the context is `window` object.

To remove a handler, we need to give the function a name.

```javascript
function f() {
  console.log("Clicked!");
}
addEventListener("click",f);
removeEventListener("click",f);
```

The handler is passed an event object, which provides additional information about the event. The information differs per type of event. However, there is always a `type` property which holds a string telling you what type of event it is.

By default, an event will propogate to the the ancestors of the context. To stop the propogation, call the `stopPropogation()` method of the event object.

The `target` property of an event object refers to the node where they originated.

Each event has a default handler, for example, following the link, scrolling down the page, etc. To prevent the default handler from functioning, call the `preventDefault()` method of the event object.

When an element gains focus, the `focus` event fires. This type of events is special in that they do not propogate.

When a page finishes loading, the `load` event fires on `window` and `document.body`.

When a page is closed, a `beforeunload` event fires. If the handler returns a string, then the string will be shown in a dialog that asks the user if they want to stay on the page or leave it.

Though events can fire at any time, no two scripts in a single document ever run at the same moment. If you really want to do some time consuming thing in the background without freezing the page, browsers provide something called web workers.

The `setTimeout` function schedules a function to be invoked after a specific number of milliseconds.

```javascript
setTimeout(function(){
  document.body.style.background = "yellow";
},1000);
```

To remove a scheduled function, give the function a name and call `clearTimeout` with the function name passed.

The `setInterval` and `clearInterval` functions are similar, and the function passed to them will be repeated every time a specific number of milliseconds is passed.
