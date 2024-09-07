---
title: Small review on CSS
description: ""
date: 2024-09-07T02:07:34.014Z
preview: ""
draft: false
tags: [css, frontend]
categories: [Web]
image: "https://images.unsplash.com/photo-1725610588150-c4cd8b88affd?q=80&w=2574&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D"
slug: small-review-on-css
---
Though I am not currently proficient in frontend, I do sometimes have to get in touch with HTML/CSS when setting up static pages. Here is some personal note on my recent refresh on CSS (last time I touched it is during my undergrad🫠).

## CSS box model
Important concepts include `margin`, `border`, `padding`, and `content`. Here is a nice illustration from Wikipedia:
![Box model definition from Wikipedia](https://upload.wikimedia.org/wikipedia/commons/7/7a/Boxmodell-detail.png)

## Float
Float is a CSS property that specifies whether a box should float to the left, right, or not at all in its parent container. It is commonly used to wrap text around images. Understand it as putting the element "out of the normal flow".

## Psuedo-classes
Psuedo-classes are used to define the special state of an element. For example, `:hover` is used to define the style of an element when the mouse is placed over it.
```css
a:link {
    color: blue;
}

a:hover {
    color: red;
}
```

## CSS Animations
The `@keyframes` rule is used to create animations, where the percentage is the time during the animation. For example, the following code will change the background color of an element from red to yellow to blue to green.
```css
@keyframes example {
    0% {background-color: red;}
    25% {background-color: yellow;}
    50% {background-color: blue;}
    100% {background-color: green;}
}
```

You can also alter the timing function of the animation using the `animation-timing-function` property, like `ease`, `linear`, `ease-in`, `ease-out`, `cubic-bezier()` family, etc.

## Responsive layout
### Media queries
Media queries are used to apply different styles for different devices, using different breakpoints. The following code will change the background color of an element to yellow when the screen width is less than 600px.
```css
@media only screen and (max-width: 600px) {
    body {
        background-color: yellow;
    }
}
```

### Relative units
In introductory courses `px` is usually used as the unit of length, but there are a bunch of other units out there that are more responsive. Those incl.:
- `vw` and `vh`: 1% of the viewport width and height, respectively.
- `em`: Relative to the font-size of the parent element.
- `rem`: Relative to the font-size of the root element.
- `vmin` and `vmax`: 1% of the viewport's smaller and larger dimension, respectively.
- `%`: Relative to the parent element.

### Logical pixels
I have once thought that how can media handle tablet displays on iPad's retina or other high-resolution tablets. 

Logical pixels is an encapsulation of the physical px in CSS, where `device-pixel-ratio` is the ratio of physical pixels to logical pixels. This allows for high-resolution displays to display content at a higher resolution without changing the layout of the page.