# Focusing: focus/blur

An element receives a focus when the user either clicks on it or uses the `key:Tab` key on the keyboard. There's also an `autofocus` HTML attribute that puts the focus into an element by default when a page loads and other means of getting a focus.

Focusing generally means: "prepare to accept the data here", so that's the moment when we can run the code to initialize or load something.

The moment of loosing the focus ("blur") can be even more important. That's when a user clicks somewhere else or presses `key:Tab` to go to the next form field, or there are other means as well.

Loosing the focus generally means: "the data has been entered", so we can run the code to check it or even to save it to the server and so on.

There are important peculiarities when working with focus events. We'll do the best to cover them here.

[cut]

## Events focus/blur

The `focus` event is called on focusing, and `blur` -- when the element loooses the focus.

Let's use them for validation of an input field.

In the example below:

- The `blur` handler checks if the field the email is entered, and if not -- shows an error.
- The `focus` handler hides the error message (on `blur` it will be checked again):

```html run autorun height=60
<style>
  .invalid { border-color: red; }
  #error { color: red }
</style>

Your email please: <input type="email" id="input">

<div id="error"></div>

<script>
*!*input.onblur*/!* = function() {
  if (!input.value.includes('@')) { // not email
    input.classList.add('invalid');
    error.innerHTML = 'Please enter a correct email.'
  }
};

*!*input.onfocus*/!* = function() {
  if (this.classList.contains('invalid')) { // сбросить состояние "ошибка", если оно есть
    this.classList.remove('invalid');
    error.innerHTML = "";
  }
};
</script>
```

Modern HTML allows to do many validations using input attributes: `required`, `pattern` and so on. And sometimes they are just what we need. Javascript can be used when we want more flexibility. Also we could automatically send the changed value on the server if it's correct.


## Methods focus/blur

Methods `elem.focus()` and `elem.blur()` set/unset the focus on the element.

For instance, let's make the visitor unable to leave the input if the value is invalid:

```html run autorun height=80
<style>
  .error {
    background: red;
  }
</style>

Your email please: <input type="email" id="input">

<script>
  input.onblur = function() {
    if (!this.value.includes('@')) { // not email
      // show the error
      this.classList.add("error");
*!*
      // ...and put the focus back
      input.focus();
*/!*
    } else {
      this.classList.remove("error");
    }
  };
</script>
```

It works in all browsers except Firefox ([bug](https://bugzilla.mozilla.org/show_bug.cgi?id=53579)).

If we enter something into the input and then try to use `key:Tab` or click away from the `<input>`, then `onblur` returns the focus back.

Please note that we can't "prevent loosing focus" by calling `event.preventDefault()` in `onblur`, because `onblur` works *after* the element lost the focus.

## Allow focusing on any element: tabindex

By default many element do not support focusing.

The list varies between browsers, but one thing is always correct: `focus/blur` support is guaranteed for elements that a visitor can interact with: `<button>`, `<input>`, `<select>`, `<a>` and so on.

From the other hand, elements that exist to format something like `<div>`, `<span>`, `<table>` -- are unfocusable by default. The method `elem.focus()` doesn't work on them, and `focus/blur` events are never triggered.

This can be changed using HTML-attribute `tabindex`.

The purpose of this attribute is to specify the order number of the element when `key:Tab` is used to switch between them.

That is: if we have two elements, the first has `tabindex="1"`, and the second has `tabindex="2"`, then pressing `key:Tab` while in the first element -- moves us to the second one.

There are two special values:

- `tabindex="0"` makes the element the last one.
- `tabindex="-1"` means that `key:Tab` should ignore that element.

**Any element supports focusing if it has `tabindex`.**

For instance, here's a list. Click the first item and press `key:Tab`:

```html autorun no-beautify
Click the first item and press Tab. Keep track of the order. Please note that many subsequent Tabs can move the focus out of the iframe with the example.
<ul>
  <li tabindex="1">One</li>
  <li tabindex="0">Zero</li>
  <li tabindex="2">Two</li>
  <li tabindex="-1">Minus one</li>
</ul>

<style>
  li { cursor: pointer; }
  :focus { outline: 1px dashed green; }
</style>
```

The order is like this: `1 - 2 - 0` (zero is always the last). Normally, `<li>` does not support focusing, but `tabindex` full enables it, along with events and styling with `:focus`.

## Delegation: focusin/focusout

Events `focus` and `blur` do not bubble.

For instance, we can't put `onfocus` on the `<form>` to highlight it, like this:

```html autorun height=80
<!-- on focusing in the form -- add the class -->
<form *!*onfocus="this.className='focused'"*/!*>
  <input type="text" name="name" value="Name">
  <input type="text" name="surname" value="Surname">
</form>

<style> .focused { outline: 1px solid red; } </style>
```

The example above doesn't work, because when user focuses on an `<input>`, the `focus` event triggers on that input only. It doesn't bubble up. So `form.onfocus` never triggers.

There are two solutions.

First, there's a funny historical feature: `focus/blur` do not bubble up, but propagate down on the capturing phase.

This will work:

```html autorun height=80
<form id="form">
  <input type="text" name="name" value="Name">
  <input type="text" name="surname" value="Surname">
</form>

<style> .focused { outline: 1px solid red; } </style>

<script>
*!*
  // put the handler on capturing phase (last argument true)
  form.addEventListener("focus", () => form.classList.add('focused'), true);
  form.addEventListener("blur", () => form.classList.remove('focused'), true);
*/!*
</script>
```

Second, there are `focusin` and `focusout` events -- exactly the same as `focus/blur`, but they bubble.

Note that they must be assigned using `elem.addEventListener`, not `on<event>`.

So here's another working variant:

```html autorun height=80
<form id="form">
  <input type="text" name="name" value="Name">
  <input type="text" name="surname" value="Surname">
</form>

<style> .focused { outline: 1px solid red; } </style>

<script>
*!*
  // put the handler on capturing phase (last argument true)
  form.addEventListener("focusin", () => form.classList.add('focused'));
  form.addEventListener("focusout", () => form.classList.remove('focused'));
*/!*
</script>
```

## Summary

Events `focus` and `blur` trigger on focusing/loosing focus on the element.

Their specials are:
- They do not bubble. Can use capturing state instead or `focusin/focusout`.
- Most elements do not support focus by default. Use `tabindex` to make anything focusable.

The current focused element is available as `document.activeElement`.