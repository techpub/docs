---
layout: default
type: guide
shortname: Docs
title: Release notes
subtitle: Guide
---

{% include toc.html %}

<style>
.breaking ::after {
  color: red;
  content "FOO!"
  }
</style>

## Release 0.9

A number of APIs changed between 0.8-rc.2 and 0.9. This document summarizes the changes.

### Element registration changes
{: .breaking }

<h4 class="breaking">Breaking change: Mixins replaced by behaviors</h4>

Mixins have been replaced by _behaviors_, which can define properties, add
lifecycle callbacks, observers, and event listeners.

Specify behaviors using the `behaviors` array on the prototype:

    Polymer({
      is: "enhanced-element",
      behaviors: [CoolBehavior]
    });

For details, see [Behaviors](behaviors.html).

#### <span class="breaking">Breaking change:</span> constructor renamed to factoryImpl

When creating a custom constructor, the configuration function is
renamed from `constructor` to `factoryImpl`, to aid compilation tools.

#### <span class="breaking>Breaking change:</span> hostAttributes changes.

Static attributes defined in `hostAttributes` can now be overridden from markup.

As a part of this change, the `class` attribute can no longer be set from 
`hostAttributes`. 

<h3><span class="breaking">Breaking change:</span> Property observer changes</h3>

The format for property observers has changed to be more like the format for computed properties:

Before:

    observers: {
      'preload src size': 'updateImage'
    },

    updateImage: function(preload, src, size) {
      // ... do work using dependent values
    }

After:

    observers: [
      'updateImage(preload, src, size)'
    ],

    updateImage: function(preload, src, size) {
      // ... do work using dependent values
    }

Also, property observers are not invoked until **all** dependent properties are defined.
If the observer is not being invoked, make sure all dependent properties have non-`undefined`
default values set.

### Styling changes

#### Custom property support

This release includes several enhancements and changes to custom property support:

*   Custom property support is enabled for all elements. The `enableCustomStyleProperties` 
    flag is no longer required.

*   <span class="breaking">Breaking change:</span> Style mixins are applied with `@apply` instead of `mixin`.

        @apply(--my-style-mixin)

*   The `var` function allows you to supply a default value, which is used if the custom
    property is not defined:

        background-color: var(--my-background, red);

*   Custom properties and mixins can be used inside a mixin.

        --foo: {
          color: var(--my-color);
          @apply(--my-theme);
        };


#### <span class="breaking>Breaking change:</span> x-style renamed to custom-style

The `custom-style` element replaces the experimental `x-style` element.

Custom properties and CSS mixins can now be applied inside a `custom-style` element.

For more details, see [Custom element for document styling](devguide/styling.html#custom-style).

#### Support for :root selector

Styling now supports the [`:root` pseudo-class](https://developer.mozilla.org/en-US/docs/Web/CSS/:root)
inside `<custom-style>`. The `:root` selector the matches the document's root scope. <<<Waiting for primer updates for more details>>>

#### <span class="breaking>Breaking change:</span> Layout classes replaced by custom properties {#layout-properties}

Layout attributes, introduced in 0.8 to replace layout classes, have been removed in favor 
of a system based on custom properties.

To use these layout properties:

1.  Install the `iron-flex-layout` component:

        bower install --save PolymerElements/iron-flex-layout

2.  Import `iron-flex-layout`:

        <link rel="import" href="bower_components/iron-flex-layout/iron-flex-layout.html">

3.  Apply the required rules anywhere in your style sheet using `@apply`:

        @apply(--layout-fit);

For example, if your 0.8 element was using:

    hostAttributes: {
      class: "layout horizontal center-center"
    }

Your 0.9 element should use:

    <dom-module id="layout-element">
      <style>
        :host {
          @apply(--layout-horizontal --layout-center-center);
        }
      </style>
      ...

For another example, see the [Migration guide](migration.html#layout-attributes).

To see the available custom layout properties, see the [`iron-flex-layout` source]
(https://github.com/PolymerElements/iron-flex-layout/blob/master/iron-flex-layout.html).
For more examples of the layout properties in use, see the 
[demo](https://github.com/PolymerElements/iron-flex-layout/blob/master/iron-flex-layout.html).

**Note:** This area may be subject to more change before 1.0.
{: .alert .alert-info }

### Data binding changes

#### <span class="breaking>Breaking change:</span> Template helper elements no longer experimental

The template helper elements are no longer experimental, and have been renamed:

*  `x-repeat` is now `dom-repeat`.
*  `x-bind` is now `dom-bind`.
*  `x-array-selector` is now `array-selector`.
*  `x-if` is now `dom-if`.

#### Nested template support

As of 0.9, nested templates can access their parent's scope. See [Nesting dom-repeat templates]
(devguide/templates.html#nesting-templates) for details.

#### <span class="breaking>Breaking change:</span> Array mutation methods

In 0.8, an array observer was used to monitor the mutation of arrays, so adding an 
item to an array was observed automatically, but changing a value in an array item required
the `setPathValue` method (now renamed to `set`).

0.9 replaces the array observers with a set of array mutation methods. For array changes
to be observed by data bindings, computed properties, or observers, you must use the provided
helper methods: `push`, `pop`, `splice`, `shift`, and `unshift`. Like `set`, the first argument
is a string path to the array.

```
this.push('users', { first: "Stephen", last: "Maturin" });
```

### Utility functions

#### <span class="breaking>Breaking change:</span> transform and translate3d API changes

The method signatures for the `transform` and `translate3d` utility methods have
changed to match the other utility methods. The `node` argument is now the last argument,
and is optional. If `node` is omitted, the methods act on `this`.

Before:

    transform(node, transform);
    translate3d(node, x, y, z);

After:

    transform(transform, node);
    translate3d(x, y, z, node);

#### <span class="breaking>Breaking change:</span> fire API changes

The `fire` method now takes three arguments:

    fire(type, [detail], [options]);

The `options` object can contain the following properties:

*   `node`. Node to fire the event on. Defaults to `this`.
*   `bubbles`. Whether the event should bubble. Defaults to `true`.
*   `cancelable`. Whether the event can be canceled with `preventDefault`. Defaults to `false`.

#### New utilities

The following utility functions were added since 0.8-rc.2 _or_ were missing 
from the earlier documentation:

*   `$$`
*   `cancelAsync`
*   `debounce`
*   `cancelDebouncer`
*   `flushDebouncer`
*   `isDebouncerActive`

For details, see [Utility functions](utility-functions.html).

### Bug fixes

Release 0.9 includes a number of bug fixes. A few notable fixes are listed below.


- The `id` attribute can now be data bound. (Note that if `id` is data bound,
  the element is omitted from `this.$`.)

- Default values are now set correctly for read-only properties.

- An identifier with two dashes in the middle (`c--foo`) was improperly interpreted
  as a CSS custom property name.
