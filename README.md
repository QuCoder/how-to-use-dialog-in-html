<h1 align="center">How to use the HTML element &lt;dialog&gt;?</h1>

In my work, I often create custom UI components or use pre-existing ones. The challenge with such components is that they are often tied to a specific framework, and their implementation requires writing complex non-standardized logic. For a long time, custom solutions were used for basic UI components, such as dialog boxes, and in more challenging cases, built-in JavaScript methods like `alert()`, `prompt()`, and `confirm()`.

The great news is that such a component can be implemented using the native HTML element `<dialog>`, which is built into the HTML5 standard and works uniformly across all modern browsers.

In the W3C Working Draft status, the `<dialog>` tag appeared in May 2013 alongside interactive elements like `<details>` and `<summary>`, designed to address classic interface tasks. Since 2014, `<dialog>` has been available only in Google Chrome and Opera, and full support in Firefox and Safari only emerged in March 2022. For this reason, `<dialog>` was relatively rarely used in real projects. However, with nearly two years of support by major browsers, the standard has become stable enough to confidently replace custom `<div class="modal" tabindex="-1" role="dialog" aria-modal="true">` with a native implementation.

Let's explore the capabilities of `<dialog>` in more detail.

**Key Features of Usage**

The HTML `<dialog>` tag creates a dialog window hidden by default on the page, which can function in two modes: as a popup or as a modal window.

Popup pop-ups are commonly used to display unobtrusive notifications, such as cookie usage messages on websites, automatically disappearing toast messages, tooltips, and even elements simulating a context menu triggered by right-click.

Modal windows are applied when it is necessary to focus the user's attention on a specific task. These can include notifications and warnings requiring user confirmation of actions on the page, complex interactive forms, and, for example, lightboxes for viewing images or videos.

A popup does not interfere with interaction with the page, unlike a modal window, which opens over the entire document, darkens the background around it, and blocks any actions with other content. This logic works without the need for additional styles and scripts; the only difference is which method is called to open the dialog.

**Methods for Opening a Dialog Window**

1. Popup pop-up:
 ```
 <dialog id="pop-up">Hello, i'm a pop-up!</dialog>
 ```
 ```
 const popUpElement = document.getElementById("pop-up");
popUpElement.show();
```
2. The modal window:
```
<dialog id="modal">Hi, I'm a modal!</dialog>
```
```
сonst modalElement = document.getElementById("modal");
modalElement.showModal();
```


In both cases, when the window is opened, the <dialog> tag is assigned the boolean attribute open with a value of true. While you can directly set the attribute value to true, in this case, the dialog window will open as a pop-up, and you won't be able to interact with it like a modal. Therefore, to render modal windows, you should use the corresponding method only. To create an initially open pop-up, you can even do without JavaScript:

```
<dialog open>Hello, i'm a pop-up!</dialog>
```

**Try it out:**

Opening a pop-up using the .show() method: [link](https://codepen.io/alexgriss/pen/zYeMKJE)

Opening a modal using the .showModal() method: [link](https://codepen.io/alexgriss/pen/jOdQMeq)

Changing the open attribute directly: [link](https://codepen.io/alexgriss/pen/wvNQzRB)

**Ways to close the dialog window:**

Dialog windows are closed in the same way, regardless of how they were opened. Here are a few ways to close a pop-up or modal window:

1. by calling the .close() method:
```
 сonst dialogElement = document.getElementById("dialog");
dialogElement.close();
```
2. through initiation of the submit event in the context of a form with attribute `method="dialog"`
```
<dialog>
  <h2>Закрой меня!</h2>
  <form method="dialog">
     <button>Закрыть</button>
  </form>
</dialog>
```

3. by Esc key

      Closing via the Esc key works only for modal windows. When closed using this method, the cancel event is triggered first, and only then the close event – this is convenient, for example, to warn the user that changes made in a form inside the modal will not be saved.


**Try it out:**

Closing the dialog window using the close method: [link](https://codepen.io/alexgriss/pen/GRzwjaV)

Closing the dialog window via form submission: [link](https://codepen.io/alexgriss/pen/jOdQVNV)

Closing the modal via pressing Esc: [link](https://codepen.io/alexgriss/pen/KKJrNKW)

Preventing the modal from closing via Esc by listening to the cancel event: [link](https://codepen.io/alexgriss/pen/mdvQObN)

**Return value on closing:**

When closing the dialog window through a form with the attribute method="dialog", you can get and process the value indicating the button that was pressed before closing. This is useful if different actions need to be performed on the page after pressing different closing buttons. To do this, you can access the returnValue property of the dialog element, which will contain the value of the value attribute of the button that the user pressed to close the window.

**Try it out:** [link](https://codepen.io/alexgriss/pen/ZEwmBKx)

## More about the mechanics:
Let's take a closer look at the mechanics of how the dialog box works and the details of the browser implementation.

**Mechanics of pop-up:**

If the <dialog> element was opened as a pop-up using the .show() method or directly by specifying the open attribute, the browser engine will automatically position the pop-up as an absolutely positioned block element where it was specified in the DOM. Basic CSS styles, including margins and borders, will be applied to this element, and the first focusable element inside the window will receive focus automatically through the global autofocus attribute. Interaction with the rest of the page will still be possible.

**Mechanics of modal window:**

The modal window is structured and operates somewhat more complexly than a pop-up.

**Document Overlay:**

When opening a modal window using the .showModal() method, the <dialog> element is rendered in a special layer of the HTML document. This layer covers the entire width and height of the visible area of the page, being positioned above the entire document. This layer is called the top layer of the document and is an internal concept of the browser – it cannot be directly controlled. In certain browsers, such as Google Chrome, each modal window is rendered in a separate DOM node of the top layer, which can be seen in the element inspector.

The concept of layers refers to the stacking context concept, which describes how elements are positioned relative to each other along the Z-axis in relation to the user in front of the screen. For example, when setting the z-index CSS property for an element, we create a stacking context confined to that element. The position of the element will be calculated relative to the positions of its neighbors, and all z-index values of child elements will only be considered within the stacking context of the parent. This hierarchy of stacking contexts can be represented as a layered structure, and an open modal window will always be at the top of this hierarchy since it is rendered in the top layer, and there is no need to set the CSS rule z-index for it.

Learn more about *stacking context*: [link](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_positioned_layout/Understanding_z-index/Stacking_context)

**Learn more about which elements are rendered in the top layer:** [link](https://developer.mozilla.org/en-US/docs/Glossary/Top_layer)

*Document Blocking:*

When the modal element is rendered in the top layer, a pseudo-element ::backdrop is created beneath it, whose dimensions are set to the current visible area of the document. This backdrop prevents actions on the rest of the page, even if the CSS rule pointer-events: none is set for it.

Additional blocking of user actions is provided by automatically setting the global inert attribute for all elements except the modal window. The inert attribute prevents the triggering of click and focus events within elements for which it is set, and also hides them from screen readers and other accessibility technologies.

Learn more about the inert attribute: [link](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/inert)

*Focus Behavior:*

The first focusable element inside the modal will automatically receive focus when the modal is opened. To change the element that will have initial focus, you can use the autofocus or tabindex attributes. Setting tabindex for the dialog element is not possible since it is, in any case, the only element on the page to which the inert attribute logic does not apply.

When closing the dialog window, the focus returns to the element that triggered its opening.

**Addressing Interaction Issues with Modal Windows:**

Unfortunately, the native implementation of the <dialog> element does not cover all aspects of interacting with modal windows. Below, I propose solutions to key UX problems that may arise when using modal windows.

*Scroll Locking:*

Although the native implementation of the modal window creates a pseudo-element ::backdrop that sits above the page and prevents interaction with the content, scrolling is still accessible. This can distract the user, so it is recommended to trim the `body` content when opening a modal window:

```
body {
  overflow: hidden;
}
```
This css-rule will have to be dynamically added and removed each time the modal window is opened and closed. This can be achieved by manipulating the class containing this CSS rule:
```
// open
document.body.classList.add("scroll-lock");
// close
document.body.classList.remove("scroll-lock");
```
You can also use the :has selector if the support status of this selector meets the requirements of the project:
```
body:has(dialog[open]) {
  overflow: hidden;
}
```

**Closing the dialog by clicking on the free area**
This is a standard UX scenario for a modal window and can be implemented in several ways. I suggest exploring two solutions to this problem:

*Method based on the features of the* `::backdrop`

   Clicking on the pseudo-element backdrop is considered as a click on the dialog element itself. Therefore, if you wrap the entire content of the modal window in an additional `<div>` and then cover the dialog element with it, you can determine whether the click was directed to the backdrop or the content of the modal window.

   Don't forget to reset the default browser styles of margins and borders for the `<dialog>` element to prevent accidentally closing the modal window when clicking on them:
```
dialog {
  padding: 0;
  border: none;
}
```

Now we apply the styling for the window's common borders and margins only to the inner wrapper.

Now, let's write a function that will close the modal window only when clicking on the backdrop, not on the inner wrapper element:

```
const handleModalClick = ({ currentTarget, target }) => {
  const isClickedOnBackdrop = target === currentTarget;

  if (isClickedOnBackdrop) {
    currentTarget.close();
  }
}

modalElement.addEventListener("click", handleModalClick);
```
*Method based on detecting dialog window dimensions*

Unlike the first method, which required wrapping the inner content of the modal window in an additional element, this method does not require the use of an additional wrapper. All that is needed is to check whether the cursor coordinates extend beyond the area of the window element when clicked:

```
const handleModalClick = (event) => {
  const modalRect = modalElement.getBoundingClientRect();

  if (
    event.clientX < modalRect.left ||
    event.clientX > modalRect.right ||
    event.clientY < modalRect.top ||
    event.clientY > modalRect.bottom
  ) {
    modalElement.close();
  }
};

modalElement.addEventListener("click", handleModalClick);
```
