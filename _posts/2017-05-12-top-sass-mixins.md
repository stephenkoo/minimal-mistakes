---
title: "Top 6 SASS Mixins, Functions & Placholders Everyone Should Use"
categories:
    - tips
tags:
    - SASS/CSS
---
I love CSS pre-processors like SASS because they make CSS so much more
functional.

(I like SCSS specifically because I don’t have to wrangle with false positive
linting warnings.)

Here are my favourite SASS mixins (and other cool SASS stuff) that I’ve found
while scouring the web for the most useful SASS tricks, as well as gems that
I’ve picked up on large-scale projects I’ve worked on.

## 0.0 What’s the difference between SASS mixins, functions, placeholders and whatnot?
I’ll be writing a post about what the differences between these are soon, but for
now, think of a `@mixin` as smart css declarations that
change depending on what inputs you put into the mixin.

Like other declarations, you use them by putting them inside css selectors.

A `@function` just computes and returns values based on inputs. You commonly use
them inside css declarations as a value in a property-value pair.

A `%placeholder` is a ghost css rule (selector + declaration) that won't produce
any css until you make a selector that `@extends placeholder`.

All selectors that extend the placeholder absorb the placeholder’s declarations.

## 1.0 Px to rem converter
```scss
// Converts one px value to rem.
// `$zero-safe-rem-value` returns a 0 if `$px-value` is 0.
@function convert-to-rem($px-value) {
  $rem-value: $px-value / $base-font-size;
  $zero-safe-rem-value: if($px-value == 0, 0, $rem-value);

  @return $zero-safe-rem-value;
}

// Converts single or multiple px values into rem.
// Accepts multiple values in the form: `rem(20px 30px 0 40px)`.
@function rem($px-values) {
  $rem-values: ();

  @each $px-value in $px-values {
    $rem-values: append($rem-values, convert-to-rem($px-value));
  }

  @return $rem-values;
}
```

## 2.0 Set rem-unit font-sizes with px-unit fallback
```scss
@mixin font-size($px-size, $base-px-size: $base-font-size) {
  font-size: $px-size;
  font-size: rem($px-size, $base-px-size);
}
```

## 3.0 Media query mixins
### 3.1 Simple media query mixins
These 3 mixins are straightforward and get the job done 90% of the time.
I’ve used them for most projects I work on.
```scss
@mixin above($width) {
  @media only screen and (min-width: $width) {
    @content
  }
}

@mixin below($width) {
  @media only screen and (max-width: $width - 1) {
    @content
  }
}

@mixin between($min-width, $max-width) {
  @media only screen (min-width: $min-width) and (max-width: $max-width - 1) {
    @content
  }
}
```
### 3.2 Advanced media query mixins
What if you wanted more control over media queries?

What if you wanted media expressions like `screen`, `orientation: landscape`, or
`min-resolution: 2dppx` to make your styles really specific to certain devices?

If so, I highly recommend Eduardo Bouça and Hugo Giraudel’s
[@include-media SASS library][include-media].

Eduardo’s done a great job explaining how their library works on
[CSS-Tricks][css-tricks approaches to media].

Here’s an example of how I like to use it:
```scss
$breakpoints: (
  'bp-1': 576px;
  'bp-2': 768px;
  'bp-3': 992px;
  'bp-4': 1200px;
) !default;

// Commonly used expression aliasses
$md1:      'screen', '>=bp-1';
$md2:      'screen', '>=bp-2';
$md3:      'screen', '>=bp-3';
$md4:      'screen', '>=bp-4';
$md0-ret2: 'screen', 'retina2x';
$md1-ret2: 'screen', 'retina2x', '>=bp-1';
$md2-ret2: 'screen', 'retina2x', '>=bp-2';
$md3-ret2: 'screen', 'retina2x', '>=bp-3';
$md4-ret2: 'screen', 'retina2x', '>=bp-4';

.example-class {
  font-size: 1rem;

  @include media($md1...) {
    font-size: 2rem;
  }
}
```

## 4.0 Hiding text overflow
```scss
// Truncates text overflow (and add ellipses by default).
@mixin truncate($overflow: ellipsis) {
  overflow: hidden;
  text-overflow: $overflow;
  white-space: nowrap;
}
```

## 5.0 Layout tools
### 5.1 Absolute position
```scss
@mixin absolute-position($top: auto, $right: auto, $bottom: auto, $left: auto) {
  bottom: $bottom;
  left: $left;
  position: absolute;
  right: $right;
  top: $top;
}
```

### 5.2 Clearfix
```scss
%clearfix {

  &::before,
  &::after {
    content: '';
    display: table;
  }

  &::after {
    clear: both;
  }
}
```

## 6.0 Fluid mixin
This mixin lets you achieve a concept commonly known as fluid-type, although I
don’t refer to it by that name because this can be used for so many other
properties other than typeface font sizes.

The concept is to linearly increase (or decrease) a value between 2 breakpoints.

This mixin is by Indrek Pass which you can [read about here][madebymike] and
[play with on Sassmeister][sassmeister].

You can see fluid type in action on [CSS-Tricks][css-tricks fluid typography].

```scss
// Strips values of their units.
// See: https://css-tricks.com/snippets/sass/strip-unit-function/
@function stripUnit($value) {
  @return $value / ($value * 0 + 1);
}

// You can replace `@media` with whatever media query mixin you're using.
@mixin fluid(
  $min-value,
  $max-value,
  $min-vw,
  $max-vw,
  $properties...
) {
  @each $property in $properties {
    #{$property}: $min-value;
  }

  @media only screen and (min-width: #{$min-vw}) {
    @each $property in $properties {
      #{$property}: calc(#{$min-value} + #{stripUnit($max-value - $min-value)} * (100vw - #{$min-vw}) / #{stripUnit($max-vw - $min-vw)});
    }
  }

  @media only screen and (min-width: #{$max-vw}) {
    @each $property in $properties {
      #{$property}: $max-value;
    }
  }
}

.example {
  @fluid(20px, 40px, 320px, 1200px, padding-top, margin-bottom)
}
```
This mixin is so cool that I’m experimenting using this fluid mixin as solution
to my quest for finding the perfect responsive vertical-spacing method (which I
might write about in the future).

[include-media]: http://include-media.com/
[css-tricks approaches to media]: https://css-tricks.com/approaches-media-queries-sass/
[css-tricks fluid typography]: https://css-tricks.com/snippets/css/fluid-typography/
[madebymike]: https://madebymike.com.au/writing/fluid-type-calc-examples/
[sassmeister]: https://www.sassmeister.com/gist/7f22e44ace49b5124eec
