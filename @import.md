# @import

The `@import` at-rule brings an external CSS file into another one. It's written at the top of the document, before any other CSS (except `@charset` and `@layer` rules), so if we try to write any other rule before `@import`, it will be invalid. Lastly, the imported CSS is interpreted as if it was inserted at the same point `@import` was written, so at the top of they stylesheet. We can also import CSS conditionally, based on users' media features or if their browser supports a specific feature.

```css
/* Imports other.css if the screen size is bigger than 600px` */
@import url("other.css") screen and (width > 600px);
```

There is one catch with `@import`, as Geoff noted in his post on [The At-Rules of CSS](https://css-tricks.com/the-at-rules-of-css/):

> "With the popularity of CSS preprocessors that support `@import`, it should be noted that they work differently than native CSS `@import` is a separate HTTP request for that file."

### Syntax

<!-- prettier-ignore-start -->
```css
@import [ <url> | <string> ]
        [ layer | layer(<layer-name>) ]?
        <import-conditions> ;

<import-conditions> = [ supports( [ <supports-condition> | <declaration> ] ) ]?
                     <media-query-list>?

```
<!-- prettier-ignore-end -->

### Arguments

```css
/* Normal Imports */
@import "other.css";
@import url("other.css");

/* Layered Imports */
@import url("other.css") layer; /* anonymous layer */
@import url("other.css") layer(my-layer); /* named layer */

/* Media-dependant Imports */
@import url("other.css") screen and (width > 600px);
@import url("other.css") screen and (prefers-color-scheme: dark);

/* Support-dependant Imports */
@import url("other.css") supports(anchor-name: --anchor);
@import url("other.css") supports((display: grid) and (grid-template-columns: subgrid));

/* All Combined */
@import url("narrow.css") layer(my-layer) supports(display: grid) screen and (width > 600px);
```

-   **`<url> | <string>`:** The location of the file we'll import. We can write just a string or enclose it in an `url` function. It can also be a local or remote path.
-   **`layer | layer(<layer-name>)`:** Using [CSS Layers](https://css-tricks.com/almanac/rules/l/layer/), we can import a stylsheet directly into a layer, either a new anonymous layer or a named layer.
-   **supports `<import-conditions>`:** Enclosed in a `supports()` wrapper, it let us insert the stylesheet based on the user's support for an specific feature. It uses the same syntax as [`@supports`](https://css-tricks.com/almanac/rules/s/supports/).
-   **media `<import-conditions>`:** It also let us insert a stylesheet conditionaly, but this time based on media features. It uses the same syntax as [`@media`](https://css-tricks.com/almanac/rules/m/media/)

### Basic usage

```css
/* other.css */

body {
	background-color: red;
}
```

```css
/* style.css */
@import url("other.css");
```

### Layered Imports

```css
/* Layered Imports */
@import url("other.css") layer; /* anonymous layer */
@import url("other.css") layer(my-layer); /* named layer */
```

### Conditional Imports

As we discussed earlier, we are only supposed to write `@import` rules at the beginning of the stylesheet, before any other rule. So importing a stylesheet walled behind a `@media` or `@supports` rule isn't allowed, but that's surely something we should be able to do, right? Think about only importing a stylesheet when the user supports a certain feature or a stylesheet including a bolder font depending on the user's `prefer-color-scheme` media feature. In the `@import` world, we **don't** write such rules like this:

```css
/* DON'T DO THIS!! */

@media screen and (width > 600px) {
	@import url("other.css");
}

/* DON'T DO THIS!! */
@supports (display: grid) {
	@import url("other.css");
}
```

Instead, `@import` has a built-in syntax to conditionally import CSS, depending if on the user's media features or browser support. To only bring a stylsheet when the user supports a certain feature, we can write the property to support inside a `supports()` wrapper function:

```css
@import url("other.css") supports(anchor-name: --anchor);
```

The next demo should only import another CSS file if your browser supports [anchor positioning](https://css-tricks.com/css-anchor-positioning-guide/):

It's also possible to

```css
@import url("other.css") screen and (width > 600px);
@import url("other.css") screen and (prefers-color-scheme: dark);
```

### Importing CSS to JavaScript

```js
import sheet from "./styles.css" assert {type: "css"};
document.adoptedStyleSheets = [sheet];
shadowRoot.adoptedStyleSheets = [sheet];
```

`@import` rules not yet allowed

### Why and why not `@import`?

In a nutshell, `@import` lets you split your CSS into several files, so that should equate to... code-splitting, cleaner file structure, things can be loaded modullary, world peace, and a big et cetera. In practice, though, you'll see a lot of hate on `@import`, reading post titles like _Avoid CSS `@import`_ or _What Is CSS `@import` And Why Can It Slow Down Websites?_. While those posts are made by performance-metrics companies, there is truth in that vanilla `@import` isn't the most performant solution, and is usually frown upon.

Let's backtrack a little, the main reason to split CSS into several files is Developer Experience; working with smaller and organized files is easier that one monster stylsheet. The problems comes when we have to load all those file into our page. Then there is two vanilla options, loading them using the everyday `link` tag and using `@import`, similar results but with a crucial difference: how the browsers loads each file. In the case of using `link`, the browser will try to load all files in parallel, while `@import` will load each file one by one, hurting performance—page load and the page may load without loading a CSS file.

**HOWEVER** this is when we are talking about the HTTP/1, since a decade ago, we live in the world of [HTTP/2](https://css-tricks.com/http2-real-world-performance-test-analysis/) which is fully multiplexed, this means that multiple files and requests can be done at the same time, so in theory we could use `@import` without worrying about perfromance. Although, I must admit I am yet to find a benchmark to back this.

### Specification

You can find the latest specification for `@import` in the [CSS Cascading and Inheritance Level 5](https://drafts.csswg.org/css-cascade-5/#at-import) module.

### Browser support

<!-- wp:css-tricks/baseline-status {"showBaselineStatus":true,"featureID":"import"} /-->

### More information

-   [Using CSS Module Scripts to import stylesheets](https://web.dev/articles/css-module-scripts) (Dan Clark)

```

```
