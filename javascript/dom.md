---
title: DOM
---

# DOM

DOM wasn't designed just for just javascript. It tries to define a language-neutral interface that can be used in other systems as well.

DOM nodes contain a wealth of links to other nearby nodes:

* childNodes
* firstChild
* lastChild
* parentNode
* previousSibling
* nextSibling

To find all offspring elements of a particular type:

```javascript
var links = document.body.getElementsByTagName("a");
for(var i = 0; i < links.length; i++)
	console.log(links[i].href);
```

To find a specific element, search the id:

```javascript
var name = document.getElementById("name");
```

To add (move) elements:

```javascript
var paragraphs = document.body.getElementsByTagName("p");
document.body.insertBefore(paragraphs[2],paragraphs[0]); // Remove the third paragraph and insert it before the first one
document.body.appendChild(paragraphs[1]); // Remove the second paragraph and insert it at the end
document.body.replaceChild(paragraphs[1],paragraphs[2]); // Remove the second paragraph, and replace the original third paragraph with the removed second paragraph
```

To create new node (text node or element node):

```javascript
document.createTextNode("Text");
document.createElement("p"); // Create a <p></p> element
```

HTML allows you to set any attribute you want on nodes. But if the name is not standard, you have to use `getAttribute` and `setAttribute` to access it.

```html
<p myattribute="something">Test.</p>
<script>
  var paras = document.body.getElementsByTagName("p");
  console.log(paras[0].getAttribute("myattribute"));
</script>
```

The position and size of an element is computed by the browser, and can be accessed by javascript.

```html
<p style="border: 3px solid red"> I'm boxed in </p>
<script>
  var para = document.body.getElementsByTagName("p")[0];
  console.log("clientHeight:", para.clientHeight);
  // These are relative to the screen
  console.log("offsetHeight:", para.offsetHeight);
  console.log("bounding box:", para.getBoundingClientRect());
  // These are relative to the page
  console.log("page X offset:", para.pageXOffset);
  console.log("page Y offset:", para.pageYOffset);
</script>
```

CSS selector can be used to find elements.

```javascript
document.querySelectorAll("p > .animal"); // returns all children of any <p> elements in class "animal"
```

