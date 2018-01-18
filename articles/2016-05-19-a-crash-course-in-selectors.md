# :ok_hand: A Crash Course in CSS Selectors: Part 1

Welcome to CSS For Engineers! This is a series of blog posts explaining concepts and features of CSS (and the preprocessor SASS) in a way that relates these new concepts back to already-familiar software engineering principles.

## The Building Blocks

To get started, there are four kinds of selectors in CSS that are the building blocks for everything else: classes, ids, elements, and the universal selector. They are the atoms and every other selector is a molecule made up of these parts.

### `.class`

The `.class` selector selects every element with that class name. It is meant for reusable components, like `.panel`, `.form-control`, etc.

For example, if you had this as your HTML

```html
<div class="fun">
  <h1>Hooray!</h1>
  <p class="special fun">Hello World!</p>
</div>
```

and this as your CSS

```css
.fun {
  padding: 10px;
  background-color: pink;
}

.special {
  color: blue;
}
```

The `div` and the `p` elements would both have `10px` padding and they would have pink backgrounds. Only the `p` tag would be colored blue because it is the only element with the class `.special`. 

### `#id`

The `#id` selector selects the only element with that ID. It is meant to be the most specific selector you can use to hone in on a very specific element and nothing else. The browser assumes that there will only be one element with a specific id in a given page.

For example, if you had this as your HTML

```html
<div></div>
<div></div>
<div id="special"></div>
<div></div>
```

and this as your CSS

```css
#special {
  background-color: yellow;
}
```

Only the element with `id="special"` would be yellow.

In most cases when building out CSS for a large and complex product, you generally won't want to use IDs. Styling IDs does not scale. Sure, today you only have one `settings` button, but what if some day you want to add another button right next to it that's styled the same way? In general, a good practice is to write all CSS as if you don't know how many elements you will want to style that way, as it is least error-prone.

### `element`
<!-- TODO(maddie): link to semantic markup article -->
This selector refers to the actualy DOM element. When you style all `div`s, you are selecting every `<div></div>` in the DOM, regardless of what class or IDs they have. If you are taking advantage of semantic markup (using `<header>` instead of `<div class="header">` for example -- more on this in its own blog post), you can use these selectors pretty effectively without needing to add extra classes.

```html
<div class="fun">
  <h1>Hooray!</h1>
  <p>Hello World!</p>
  <p class="fun">This is fun.</p>
</div>
```

and this as your CSS

```css
p {
  background-color: pink;
}
```

The two paragraphs will have pink backgrounds, but the heading will not.

### attribute selectors

You can also add specificity to these selectors by taking advantage of certain attributes the DOM element has. For instance, if you were styling a form that had both `<input type="text"></input>` and `<input type="submit"></input>`, you would want to style them differently as one appears in the DOM as a text box while the other appears as a button, despite them both having the tag name `input`.

In the CSS, this would look like:

```css
input[type="text"] {
  /* styling goes here */
}

input[type="submit"] {
  /* styling goes here */
}
```

<!-- TODO(maddie): write a post about attribute selectors -->
Generally, you can select on any attribute of the DOM element, not just `type`. There are also a bunch of really cool things you can do with attribute selectors, like comparing prefixes and suffixes. Check the [documentation](https://developer.mozilla.org/en-US/docs/Web/CSS/Attribute_selectors) for more information.

### `*`, the Universal Selector

In many ways the exact opposite of `#id`, `*` selects literally *every* element in the DOM. Use this with caution.

## Naming Classes and IDs

It is important to choose the names you give classes and ID's carefully. Syntactically, its usually best to use lowercase words connected by hyphens like `.class-name` (fun fact: this is called kebab case). You want to pick class names and IDs the same way you would pick a variable name in any other coding project, keeping them specific, consistent, and easily extendable.

## Putting Things Together

You can accomplish a lot with just the simple selectors outlined above, but sometimes you want to get fancy and complex with your CSS. You can chain together selectors to be more specific.

For example, if you had this as your HTML

```html
<p>
  <span>Hello</span>
  World
</p>
```

and this as your CSS

```css
p span {
  font-weight: bold;
}
```

You would get something that looked like this:

**Hello** World

You can also do something like this:

```css
form * {
  /* Insert styling here */
}
```

to select all descendants of the `form`, including children, "grandchildren", etc.

This is called a descendant selector, because you are selecting the descendant (`<span>`) of the parent (`<p>`). The selectors read from outside in, so first looking at all DOM elements that are `<p>`s, then inside searching for `<span>` elements. This is useful when you want to override default styling for a special component like a panel or a modal.

### Combinators

What if you want something more specific than just searching for descendants of a parent element? There are combinators, or operations you can do on selectors to get funky with your code and generate some cool stuff.

#### Concatenating Selectors

You can increase the specificity of a selector by concatenating two core building blocks together. This selects only the elements that apply to both conditions.

Examples:

`div.special` selects all elements that look like `<div class="special"></div>`

`.class-name.other-class-name` selects all elements that have `class="class-name other-class-name"` in their HTML.

`input[type="text"]` selects all elements that look like `<input type="text"></input>`. This is a special case utilizing what is called an attribute selector, where it picks up other properties of the DOM element, in this case `type`. This works for most attributes, but its better to avoid this except when it affects the HTML that is displayed, like when using types for different `input` tags.

#### >

`>` is the direct child selector. It will only select elements that are directly below, or direct descendants, of its parent.

For example, if you had this as your HTML

```html
<div>
  <span class="bar">
    <p>Hooray!</p>
  </span>
  <p>Hello</p>
</div>
<p>World</p>
```

and this as your CSS

```css
div > p {
  font-weight: bold;
}
```

Only the word "Hello" would be styled.

#### ~

`~` is the general sibling selector. So, in the case of `E ~ F` any F elements that are at the same level as E, and exist within the same parent will be selected.

For example, if you had this as your HTML

```html
<p>Yay!</p>
<div class="selected">
  <p>Hello</p>
</div>
<p>small</p>
<p>World</p>
```

and this as your CSS

```css
.selected ~ p {
  font-weight: bold;
}
```

The words "World" and "small", but neither "Hello" nor "Yay!", would be bolded this time, because the  `<p>`s are at the same level as the `<div class="selected">`, comes after the element with the `.selected` class,  and have the same parent, which may be `<body>` in this case. 

#### +

`E + F` is the adjacent sibling selector, so only the single sibling, if it matche F, coming immediately following the element matching E will be chosen. The sibling is only selected if it is the next element immediately following the first element in the selector.

For example, if you had this as your HTML

```html
<div class="selected"></div>
<h1>Testing</h1>
<p>Yay!</p>
<div class="selected">
  <p>Hello</p>
</div>
<p>World</p>
```

and this as your CSS

```css
.selected + p {
  font-weight: bold;
}
```

Only the word "World" would be bold. Take special note that "Yay!" is not bolded because there is another element between it and `<div class="selected">`.

**Keep an eye out for [Part 2](2016-05-23-selectors-part-2-pseudo-classes.md) of this blog post where I will discuss pseudo elements.**
