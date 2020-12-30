---
layout: post
title: Learn HTML and CSS (codecademy)
date: 2020-07-26
categories: [codecademy]
---

This post is a note for what I have learned from
- [codecademy - Learn HTML](https://www.codecademy.com/learn/learn-html)
- [codecademy - Learn CSS](https://www.codecademy.com/learn/learn-css)
- [codecademy - Learn Responsive Design](https://www.codecademy.com/learn/learn-responsive-design)

# HTML

## Form

### datalist

```html
<form>
  <label for="city">Ideal city to visit?</label>
  <input type="text" list="cities" id="city" name="city">

  <datalist id="cities">
    <option value="New York City"></option>
    <option value="Tokyo"></option>
    <option value="Barcelona"></option>
    <option value="Mexico City"></option>
    <option value="Melbourne"></option>
    <option value="Other"></option>  
  </datalist>
</form>
```

 The `<input>` creates a text field that users can type into and filter options from the `<datalist>`

## Semantic tags

main - section - article

# CSS

## Grid System

## Transition

```css
a {
  transition-property: background-color;
  transition-duration: 2s;
  transition-delay: 500ms;
  transition-timing-function: ease-out;
}
```

transition-timing-function: ease-in, ease-out, ease-in-out, linear

### shorthand

```css
transition: color 1.5s linear 0.5s;
```

The properties must be specified in this order: `transition-property`, `transition-duration`, `transition-timing-function`, `transition-delay`

#### multiple transitions

```css
transition: color 1s linear,
font-size 750ms ease-in 100ms;
```

```css
transition: all 1.5s linear 0.5s;
```



## Responsive Design

### relative measurement

hard coded measurement `px`<br>
use relative measurement instead

#### em

the `em` represents the size of the base font being used

> if the base font of a browser is 16 pixels (which is normally the default size of text in a browser)
> then 1 em is equal to 16 pixels. 2 ems would equal 32 pixels

#### rem

Rem stands for root em
instead of checking parent elements to size font, it checks the root element (the `html` tag)

#### percentage

size non-text HTML elements relative to their parent element

Percentages can also be used to set the padding and margin of elements

### scaling images and videos

#### overflow: hidden

Setting overflow to hidden ensures that any content with dimensions larger than the container will be hidden from view

#### height: auto

meaning an image’s height will automatically scale proportionally with the width

#### inline: block

prevent images from attempting to align with other content on the page

#### background-size: cover

cover the entire background of the element, all while keeping the image in proportion

### Media Queries

```css
@media only screen and (max-width: 480px) {
  body {
    font-size: 12px;
  }
}
```

`@media` — begins a media query rule and instructs the CSS compiler on how to parse the rest of the rule

`only screen` — Indicates what types of devices should use this rule (screen, print, handheld)<br>
screen is the media type always used for displaying content, no matter the type of device<br>
only keyword is added to indicate that this rule only applies to one media type (screen)

`and (max-width : 480px)` — media feature, the conditions that must be met in order to render the CSS<br>
instructs the CSS compiler to apply the CSS styles to devices with a width of 480 pixels or smaller

```css
@media only screen and (min-width: 320px) and (max-width: 480px) {
    /* ruleset for 320px - 480px */
}
```

#### Dots Per Inch (DPI)

supply higher quality media (images, video, etc.) only to users with screens that can support high resolution media

```css
@media only screen and (min-resolution: 300dpi) {
    /* CSS for high resolution screens */
}
```
#### require multiple media features

```css
@media only screen and (max-width: 480px) and (min-resolution: 300dpi) {
    /* CSS ruleset */
}
```

the browser will require both media features to be true before it renders the CSS within the media query

```css
@media only screen and (min-width: 480px), (orientation: landscape) {
    /* CSS ruleset */
}
```

apply a style when only one of the below is true:

- The screen is more than 480 pixels wide
- The screen is in landscape mode

`orientation` media feature detects if the page has more width than height ( `landscape` ,  `portrait` )

### Break points

- Desktop > 1600 px
- Laptop  1024px ~ 1600px
- Tablet Landscape 768px ~ 1024px
- Tablet Portrait 480px ~ 768px
- Mobile Portrait < 480px

![Break points](https://s3.amazonaws.com/codecademy-content/courses/freelance-1/unit-5/screen-sizes.png)

