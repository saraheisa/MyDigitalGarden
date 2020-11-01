---
title: CSS & Motion Design
description: Creating smooth animations with CSS
date: 2020-10-31
tags:
  - CSS
  - animation
  - design
layout: layouts/post.njk
image: https://modicum.agency/wp-content/uploads/sites/6/2017/12/Modicum_MotionReel_2017_Thumbnail-1.png
---

## CSS Transitions

how a property should display when given a different value

Are all about going from one value to another

to write it you need two things:

- property that you're changing
- the duration
- timing function *optional default ease*
- delay optional *default 0*

```css
transition: color 200ms ease-in 100ms;
```

using *ms* is more robust than *s* because JS deals with *ms*

An example of moving a ball

```css
.ball {
  transition: transform 500ms;
}

.playing .ball {
  transform: translatex(200px);
}
```

then you add playing class to the body after the page is loaded

```jsx
document.addEventListener("DOMContentLoaded", (event) => {
 document.querySelector(".parent").classList.add("playing");
});
```

### Transition multiple properties

you can do this to animate all the properties

```css
transition: all 200ms;
```

But wait don't!

this is not performant at all

here's a better way to do it

```css
transition: 
	transform 500ms 200ms,
	color 500ms;
```

### Duration

Three timeframes for user attention

- 100ms instantaneous hard to notice
- 1s noticed but okay
- 10s Noooooo boring

so for user to notice change it should be between 250ms and 300ms

### Timing Function / easing

describes an animation rate of change over time

*types of timing functions*

**`ease-in`**

acceleration starts slow then gets quicker

**`ease-out`**

deceleration starts quick then gets slower

**`ease-in-out`**

starts and ends slow and speeds up in the middle

**`Linear`**

constant rate of change

**`Steps`**

A special timing function

you give it the number of steps x 

and it splits the keyframes into x equal steps then hops between them

**`cubic-bezier`**

*create your unique animation*

it lets give the browser a set of values to say what you want that rate of change to be when do you want to speed up and when do you want to slow down

## CSS Animation

consists of

- keyframe: contains the changes
- duration
- timing function *optional*
- delay *optional*
- number of times to run *optional*

```css
animation: black-to-white 1s linear 1;

@keyframes black-to-white {
	0% { background: #000 }
	100% { background: #fff }
}
```

P.S: it always eats the last step so you may make your steps less by one

P.P.S: don't call your keyframes `running` it's a trap!!!

### More Animation Properties

`fill-mode`

When animations are done the element just goes to its normal state unless you use this

It has different values

- **backwards:** retain 0% of the animation before animating
- **forwards:** retain 100% of the animation after animating
- **both:** backward & forward together
- **none:** nothing *default*

```css
animation: black-to-white 1s linear 1 backward;
```

`play-state`

from its name it's the state of the animation 

it has two values

- running *default*
- paused

you can use `paused` value to run animation only on hover or maybe pause on hover!

`direction`

When animations goes from 0% to 100% then 0% to 100% and so on

you can change that with direction

values:

- Normal: the default
- alternate: will run from 0% to 100% then 100% to 0% and so on like a pendulum back and force
- reverse: will run from 100% to 0% always
- alternate-reverse: will run from 100% to 0% then 0% to 100% and so on

# Why using animations

smoothstate.js

dynamically load each page using AJAX

## When to use transition

- Change in task flow location
- Where you has been
- Where you're now

## Supplemental animation used for

Animation for secondary content like alerts menu

- Change in information
- Show users what is possible
- show important alerts to user that they need to overlooked

## Causal effects

like spinners hover 

## Decorative animation

doesn't add any information

## Jump Cut

from films when the camera cuts directly to a different perspective
